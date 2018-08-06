---
layout: post
title: Looping in Ansible Evaluates Things
---

I recently came across a bit of an unusual situation in Ansible that I haven't
had much luck googling, so here's something to help the next person.

I was trying to loop through a series of dictionaries, running a task with
each dictionary defining the majority of the variables that the task would use.
I had some global variables, some that varied from dictionary to dictionary,
and some that were defined inside the task. I wanted to reference the variables
in the task scope in my dictionaries - so, in my dictionaries I had a template
for a deploy directory. It would be used multiple times with different
values in the variables it references.

Here's the short version of my findings: Ansible evaluates anything you loop
over at the setup time of the loop, including everything in a dictionary,
rendering any template string inside.

Here's some more detail.

So, obviously, this task would fail - `exists_in_task_scope` hasn't been
defined.

```
# example.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
  tasks:
    - debug:
        var: exists_in_global_vars
    - debug:
        var: dict_for_looping

# Output
PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [debug] *******************************************************************
ok: [default] => {
    "exists_in_global_vars": "/home/vagrant/loop_test"
}

TASK [debug] *******************************************************************
ok: [default] => {
    "dict_for_looping": "VARIABLE IS NOT DEFINED!"
}

PLAY RECAP *********************************************************************
default                    : ok=3    changed=0    unreachable=0    failed=0
```

While this works great - when we run debug, we evaluate our template strings
the variable exists.

```
# example.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    exists_in_task_scope: superb
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
  tasks:
    - debug:
        var: exists_in_global_vars
    - debug:
        var: dict_for_looping

# output
PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [debug] *******************************************************************
ok: [default] => {
    "exists_in_global_vars": "/home/vagrant/loop_test"
}

TASK [debug] *******************************************************************
ok: [default] => {
    "dict_for_looping": {
        "first": {
            "a_var": "/home/vagrant/loop_test/superb/after"
        },
        "second": {
            "a_var": "/home/vagrant/loop_test/superb/after"
        }
    }
}

PLAY RECAP *********************************************************************
default                    : ok=3    changed=0    unreachable=0    failed=0
```

Now, let's look at looping through this dictionary. If I try to loop through
it and display a message, we see that the task is run with an item that's been
evaluated:

```
# loops.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    exists_in_task_scope: superb
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
  tasks:
    - debug:
        msg: Hello
      with_dict: "{{ dict_for_looping }}"

# output
LAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [include_role : loops] ****************************************************

TASK [loops : debug] ***********************************************************
ok: [default] => (item={'value': {u'a_var': u'/home/vagrant/loop_test/superb/after'}, 'key': u'second'}) => {
    "msg": "Hello"
}
ok: [default] => (item={'value': {u'a_var': u'/home/vagrant/loop_test/superb/after'}, 'key': u'first'}) => {
    "msg": "Hello"
}

PLAY RECAP *********************************************************************
default                    : ok=2    changed=0    unreachable=0    failed=0
```

And if I strip our task scope variable, the task fails, even though we never
explicitly access `a_var`:
```
# loops.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
  tasks:
    - debug:
        msg: Hello
      with_dict: "{{ dict_for_looping }}"

# output
PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [debug] *******************************************************************
fatal: [default]: FAILED! => {"msg": "'exists_in_task_scope' is undefined"}
        to retry, use: --limit @/Users/mac/code/ansible-loop-resolution/loops.retry

PLAY RECAP *********************************************************************
default                    : ok=1    changed=0    unreachable=0    failed=1
```

The same is true of a `loop` instead of `with_dict`.

You can't even loop through the keys of the dictionary:

```
# loops.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
  tasks:
    - debug:
        msg: Hello
      loop: "{{ dict_for_looping.keys() }}"

# output
PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [debug] *******************************************************************
fatal: [default]: FAILED! => {"msg": "'exists_in_task_scope' is undefined"}
        to retry, use: --limit @/Users/mac/code/ansible-loop-resolution/loops.retry

PLAY RECAP *********************************************************************
default                    : ok=1    changed=0    unreachable=0    failed=1
```

You can't even get away with putting the keys into a separate variable, like
this:

```
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    dict_for_looping:
      first:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
      second:
        a_var: "{{ exists_in_global_vars }}/{{ exists_in_task_scope }}/after"
    dict_keys: "{{ dict_for_looping.keys() }}"
  tasks:
    - debug:
        msg: Hello
      loop: "{{ dict_keys }}"
```

So, in summary, anything you loop over gets evaluated at loop setup time, and
as such you can't use it for templating things using templates within your
looping variables.

Here's how I turned this around and solved my particular problem: I made a
template that I would only call when I had an item in my scope, and referred to
that repeatedly. Like so:

```
# loops.yml
---
- hosts: all
  vars:
    exists_in_global_vars: "/home/vagrant/loop_test"
    template: "{{ exists_in_global_vars }}/{{ item.value.another_var }}/after"
    dict_for_looping:
      first:
        another_var: first
      second:
        another_var: second
  tasks:
    - debug:
        msg: "Hello {{template}}"
      loop: "{{ dict_for_looping | dict2items }}"

# output
PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [debug] *******************************************************************
ok: [default] => (item={'key': u'second', 'value': {u'another_var': u'second'}}) => {
    "msg": "Hello /home/vagrant/loop_test/second/after"
}
ok: [default] => (item={'key': u'first', 'value': {u'another_var': u'first'}}) => {
    "msg": "Hello /home/vagrant/loop_test/first/after"
}

PLAY RECAP *********************************************************************
default                    : ok=2    changed=0    unreachable=0    failed=0
```

Hope that helps.
