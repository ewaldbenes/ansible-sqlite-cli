# Ansible Role for SQLite CLI

This Ansible Role installs SQLite on Debian-based OSes (Debian, Ubuntu).

More specifically, it **compiles and installs the shell command** `sqlite` of SQLite from the official source code. It enables access to SQLite database files via the `sqlite` command. Other than that, SQLite does not require any installation because it is (most often) embedded into the application.

The command is installed in `/usr/local/bin/sqlite` which should be on the `PATH` environment variable, thus accessible from the terminal.



## Who is this for?

For server-running web applications that use SQLite as its database and the administrator needs to access database files for backups, migrations, and other maintenance work from a terminal. As an example, for backing up an in-use database execute `sqlite foo.db '.backup foo_19870102.db'`.



## Why another Role?

Indeed there are many Ansible Roles which install SQLite. To the author's current knowledge, all of them do it through the OS-specific package manager. Especially on Debian-based long-term-maintenance OS releases, which the author uses, the SQLite version lacks behind - sometimes years - the currently released one.

SQLite is very customizable at compile time. Additionally, it is very friendly to and even encourages you to compiling it yourself. If you install it via the OS package manager than you are at the package maintainers' mercy what they include.



## Usage

> Hint: This repository uses Ansible's recommended [directory layout](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#directory-layout) and this usage guide assumes that you do so too.

1. Download this project as a ZIP file,
2. Copy the `sqlite` role directory into your project (perhaps side-by-side to other roles)
3. (Optional) Change the version of SQLite in `sqlite/vars/main.yml` or adapt the compile arguments in `sqlite/tasks/main.yml`
3. Include the role in your playbook via adding a list entry to the `roles` key:
```
roles:
    - ...
    - sqlite
    - ...
```


## Skipping of unnecessary tasks

This Role is designed in such a way that it can be executed multiple times without affecting the correct end result. The desired result of this Role is to have the specified version of the `sqlite` command installed in the destination directory. It only downloads the source code ZIP if the required SQLite version is different from the currently installed version.
In other words, viewed from this perspective, the Role is idempotent.



## Constraints

1. the Ansible host requires Debian or a Debian-based OSes because we use the `apt` package manager and
2. the host (= remote computer) needs internet access to `https://www.sqlite.org`.

All other required tools are added by this Role.



## Customizations

It should be relatively easy to adapt this Role to other OSes with package managers like RPM or even BSD.



## How it works

* Installs tools for unpacking and compiling the SQLite amalgamation source code: `libreadline-dev`, `libncurses-dev`, `unzip`, `build-essential`,
* downloads the SQLite malgamation source code from `www.sqlite.org` to `/tmp`,
* extracts the source code to `/tmp`, compiles it
* copies the `sqlite` executable to `/usr/local/bin/sqlite`

> Hint: This Role does not clean up any intermediate files or directories inside `/tmp`.


## Background information

SQLite itself is often statically linked into an executable. Sometimes, applications use a shared library (also dynamic library) of SQLite and link to it at runtime. Therefore each application usually ships its own version of the SQLite code and is completely independent from what this Ansible role sets up. The only thing that the application and this shell utility have in common is that they are capable of reading the same database files.


## Origin of this Role

The author uses this Ansible Role for setting up the server which runs a web application. The application uses SQLite as its main database and currently serves around 185k uncached requests per month which reach the database.


## Contributions

This Role should be kept to only installing the SQLite CLI from the source. Making it work on OSes with other package manager than `apt`, or introducing more customization variables are well in-scope. If you have improvement suggestions, please open a PR!

