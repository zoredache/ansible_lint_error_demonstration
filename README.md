Demonstrate ansible-lint problems when using apply with include_tasks

For bug: https://github.com/ansible/ansible-lint/issues/507

Install dev version of ansible lint and verify version

    root@af9d88adc869:/project# pip3 install git+https://github.com/ansible/ansible-lint.git
    Collecting git+https://github.com/ansible/ansible-lint.git
    ...
    Running setup.py bdist_wheel for ansible-lint ... done                                                                        Stored in directory: /tmp/pip-ephem-wheel-cache-6q2sr0zx/wheels/24/6b/b6/80d9dadba73bf01297972aeb27a66858be08b7e808fa04ccdf
    Successfully built ansible-lint
    Installing collected packages: typing-extensions, ansible-lint
    Found existing installation: ansible-lint 4.2.0
        Uninstalling ansible-lint-4.2.0:
        Successfully uninstalled ansible-lint-4.2.0
    Successfully installed ansible-lint-4.3.0a5.dev18+ge675aa2 typing-extensions-3.7.4.2

    root@af9d88adc869:/project# ansible-lint --version
    ansible-lint 4.3.0a5.dev18+ge675aa2

( cd wo_apply ; ansible-playbook playbook_inc_role.yml playbook_inc_tasks.yml )

    # ( cd wo_apply ; ansible-playbook playbook_inc_role.yml playbook_inc_tasks.yml )
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

    PLAY [localhost] *************************************************************************************************************

    TASK [playbook to include role] **********************************************************************************************

    TASK [example : include_tasks] ***********************************************************************************************
    included: /project/wo_apply/roles/example/tasks/debug.yml for localhost

    TASK [example : debug] *******************************************************************************************************
    ok: [localhost] => {
        "msg": "Hello world!"
    }

    PLAY RECAP *******************************************************************************************************************
    localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


    PLAY [localhost] *************************************************************************************************************

    TASK [simple include task with apply] ****************************************************************************************
    included: /project/wo_apply/debug.yml for localhost

    TASK [debug] *****************************************************************************************************************
    ok: [localhost] => {
        "msg": "Hello world!"
    }

    PLAY RECAP *******************************************************************************************************************
    localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

( cd using_apply ; ansible-playbook playbook_inc_role.yml playbook_inc_tasks.yml )

    # ( cd using_apply ; ansible-playbook playbook_inc_role.yml playbook_inc_tasks.yml )
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

    PLAY [localhost] *************************************************************************************************************

    TASK [playbook to include role] **********************************************************************************************

    TASK [example : include_tasks] ***********************************************************************************************
    included: /project/using_apply/roles/example/tasks/debug.yml for localhost

    TASK [example : debug] *******************************************************************************************************
    ok: [localhost] => {
        "msg": "Hello world!"
    }

    PLAY RECAP *******************************************************************************************************************
    localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


    PLAY [localhost] *************************************************************************************************************

    TASK [simple include task with apply] ****************************************************************************************
    included: /project/using_apply/debug.yml for localhost

    TASK [debug] *****************************************************************************************************************
    ok: [localhost] => {
        "msg": "Hello world!"
    }

    PLAY RECAP *******************************************************************************************************************
    localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


( cd wo_apply ; ansible-lint -vvv playbook_inc_role.yml )

    # ( cd wo_apply ; ansible-lint -vvv playbook_inc_role.yml )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['playbook_inc_role.yml'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    DEBUG    Examining playbook_inc_role.yml of type playbook
    DEBUG    Examining roles/example/tasks/main.yml of type tasks
    DEBUG    Examining roles/example/tasks/debug.yml of type tasks

( cd using_apply ; ansible-lint -vvv playbook_inc_role.yml )

    # ( cd using_apply ; ansible-lint -vvv playbook_inc_role.yml )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['playbook_inc_role.yml'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    Traceback (most recent call last):
      File "/usr/local/bin/ansible-lint", line 10, in <module>
        sys.exit(main())
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/__main__.py", line 115, in main
        matches.extend(runner.run())
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/runner.py", line 74, in run
        for child in ansiblelint.utils.find_children(arg, self.playbook_dir):
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 163, in find_children
        basedir, item, playbook[1], playbook_dir):
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 141, in func_wrapper
        return func(*args, **kwargs)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 215, in play_children
        return delegate_map[k](basedir, k, v, parent_type)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 225, in _include_children
        result = path_dwim(os.path.join(os.path.dirname(basedir)), v)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 73, in path_dwim
        return dl.path_dwim(given)
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/dataloader.py", line 186, in path_dwim
        given = unquote(given)
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/quoting.py", line 29, in unquote
        if is_quoted(data):
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/quoting.py", line 24, in is_quoted
        return len(data) > 1 and data[0] == data[-1] and data[0] in ('"', "'") and data[-2] != '\\'
    KeyError: 0

( cd wo_apply ; ansible-lint -vvv playbook_inc_tasks.yml )

    # ( cd wo_apply ; ansible-lint -vvv playbook_inc_tasks.yml )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['playbook_inc_tasks.yml'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    DEBUG    Examining playbook_inc_tasks.yml of type playbook
    DEBUG    Examining debug.yml of type tasks

( cd using_apply ; ansible-lint -vvv playbook_inc_tasks.yml )

    # ( cd using_apply ; ansible-lint -vvv playbook_inc_tasks.yml )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['playbook_inc_tasks.yml'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    DEBUG    Examining playbook_inc_tasks.yml of type playbook

( cd wo_apply ; ansible-lint -vvv roles/example )

    # ( cd wo_apply ; ansible-lint -vvv roles/example )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['roles/example'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    DEBUG    Examining roles/example/tasks/main.yml of type tasks
    DEBUG    Examining roles/example/tasks/debug.yml of type tasks

( cd using_apply ; ansible-lint -vvv roles/example )

    # ( cd using_apply ; ansible-lint -vvv roles/example )
    DEBUG    Logging initialized to level 10
    DEBUG    Options: Namespace(colored=True, config_file=None, display_relative_path=True, exclude_paths=[], format='plain', listrules=False, listtags=False, parseable=False, parseable_severity=False, playbook=['roles/example'], quiet=False, rulesdir=[], skip_list=[], tags=[], use_default_rules=False, verbosity=3)
    DEBUG    Loading rules from /usr/local/lib/python3.7/dist-packages/ansiblelint/rules
    Traceback (most recent call last):
      File "/usr/local/bin/ansible-lint", line 10, in <module>
        sys.exit(main())
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/__main__.py", line 115, in main
        matches.extend(runner.run())
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/runner.py", line 74, in run
        for child in ansiblelint.utils.find_children(arg, self.playbook_dir):
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 163, in find_children
        basedir, item, playbook[1], playbook_dir):
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 141, in func_wrapper
        return func(*args, **kwargs)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 215, in play_children
        return delegate_map[k](basedir, k, v, parent_type)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 225, in _include_children
        result = path_dwim(os.path.join(os.path.dirname(basedir)), v)
      File "/usr/local/lib/python3.7/dist-packages/ansiblelint/utils.py", line 73, in path_dwim
        return dl.path_dwim(given)
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/dataloader.py", line 186, in path_dwim
        given = unquote(given)
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/quoting.py", line 29, in unquote
        if is_quoted(data):
      File "/usr/local/lib/python3.7/dist-packages/ansible/parsing/quoting.py", line 24, in is_quoted
        return len(data) > 1 and data[0] == data[-1] and data[0] in ('"', "'") and data[-2] != '\\'
    KeyError: 0
