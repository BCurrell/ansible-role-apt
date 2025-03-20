# Ansible Role - APT

Ansible role to control APT repositories and associated settings.

- [Ansible Docs - ansible.builtin.apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)

Since `apt-key` has been deprecated, you should not still use [ansible.builtin.apt_key](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html); it is only still maintained for backwards compatibility. Instead, this role will set up the current recommended key location and settings based on [Debian's wiki](https://wiki.debian.org/DebianRepository/UseThirdParty).

APT has supported the Deb822 repository format since v1.1 (2015). Since the currently supported versions of Debian (and most of Ubuntu) were released after this release, they all support this format. This role will not use [ansible.builtin.apt_repository](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html), as it creates APT source files with the older single-line `.list` format. The latest versions of APT are deprecating this format, so it should not be used anymore. However the deprecation of the single-line format is recent, and [ansible.builtin.deb822_repository](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/deb822_repository_module.html) was only released with Ansible 2.15. This role will instead manually set up these source files rather than using the builtin module. In future, this role will be updated to use the builtin module rather than re-inventing the wheel.

## Usage

**NOTE**: This role works by defining all of the Ansible module arguments within the inventory, with minimal parsing. This is unsafe (see [Ansible Docs - argsplat](https://docs.ansible.com/ansible/devel/reference_appendices/faq.html#argsplat-unsafe)). There is a way around this by disabling `INJECT_FACTS_AS_VARS` (see [Ansible Docs - config](https://docs.ansible.com/ansible/devel/reference_appendices/config.html#inject-facts-as-vars)). This prevents the host facts from being injected as variables. They are still accessible via `ansible_facts`.

This role is not currently on Ansible Galaxy. I'm not sure if it every will be. Instead, I recommend using a Git submodule to add this to your playbook:

```none
git submodule add https://github.com/BCurrell/ansible-role-apt.git roles/apt
```

To pull in new changes:

```none
git submodule update --remote --recursive --checkout
```

To remove the submodule:

```none
git rm roles/apt
```

When the submodule is cloned, you can use it like any other Ansible role:

```yaml
---
- name: 'Example Playbook'
  hosts: 'all'

  roles:
    # Option 1 - Pass the name as a string
    - 'apt'
    # Option 2 - Pass the name to they 'role' key with additional arguments
    - role: 'apt'
      vars: {}

  tasks:
    # Option 3 - Use the ansible.builtin.include_role module
    - name: 'Include apt role'
      ansible.builtin.include_role:
        name: 'apt'
      vars: {}
```

## Development

To avoid installing global packages to my OS, I use Poetry (v2.0 with PEP508 support) to install my development requirements:

- `pre-commit` to automate formatting and linting
- `ansible-lint` to lint the Ansible role
- `black`, `isort`, `flake8`, etc to format / lint any Python (e.g. filter plugins)

To install the development virtual environment, run `poetry install -E dev`.

If you make a PR, please run `pre-commit` if possible in one of the following ways, in order of recommendation / preference:

- `poetry run pre-commit run` (after every `git add`)
    - Use the poetry environment to run pre-commit once.
- `poetry run pre-commit install` (once after `git clone`)
    - Use the poetry environment to install pre-commit hooks to .git, meaning pre-commit will automatically run when you stage and commit changes to Git.
- `pre-commit run` (after every `git add`)
    - Use a globally installed pre-commit to run pre-commit once.
- `pre-commit install` (once after `git clone`)
    - Use a globally installed pre-commit to install pre-commit hooks to .git, meaning pre-commit will automatically run when you stage and commit changes to Git.

## Notes
