# ⏱ Taskit 📝

> Small command-line tool to create, time and track completion of your every days tasks

Summary

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
  - [Docker](#docker)
  - [Run from binary](#run-from-binary)
  - [Run from sources](#run-from-sources)
- [Getting Started](#getting-started)
  - [Initialization](#initialization)
  - [Configuration](#configuration)
  - [Task Management](#task-management)
    - [Manage Tags](#manage-tags)
    - [Show task information](#show-task-information)
  - [Task Progress](#task-progress)
    - [Task Status and Time Tracking](#task-status-and-time-tracking)
- [Changelog](#changelog)
- [License](#license)

## Overview

Keeping track of daily tasks and the amount of time we spend on each is a very taxing activity for ones brain, that's why TODO apps are so popular 📈...

So here it is... **Taskit** yet another CLI ⏱ Todo 📝 app for the fun of it 😅.

## Features

- [ ] Track tasks progress
- [ ] Tags to categorize tasks
- [ ] Track time spent
- [ ] Task Checklist

## Installation

### Docker

Using docker is the preffered installation method

```shell
user@localhost:~$ docker run --rm -v $(pwd):/app -w /app axeldeau/taskit:1.0.0 version
Taskit:
  Version:    1.0.0
  Commit:     ec4ca545
  Branch:     prod
  BuildDate:  Sat 17 Jul 2021 08:34:20 PM EDT
```

Don't forget to create an alias in your shell configuration file.

`alias taskit='docker run --rm -v $(pwd):/app -w /app axeldeau/taskit:1.0.0'`

### Run from binary

Running the folling command will install the **Taskit** binary in your $GOPATH/bin folder.

`curl -sSfL https://raw.githubusercontent.com/axeldeau/taskit/prod/install.sh | sh -s -- -b $(go env GOPATH)/bin 1.0.0`

### Run from sources

`go install github.com/axeldeau/taskit`

## Getting Started

### Initialization

Taskit needs to be initialized to be configure and store task data,

To initialize it:

```shell
user@localhost:/home/project/repo$ taskit init
Taskit has been initialized at "/home/project/repo/.taskit"
```

### Configuration

You can toogle the field available in the tasks, along with types and enums of their value.

**config** command edit the currently active configuration

```shell
user@localhost:/home/project/repo$ taskit config
  title: 
    enabled: true (mandatory)
    type: string
  tag:
    enabled: true (mandatory)
    type: string
  status:
    enabled: true (mandatory)
    type: enum
    values:
    - Todo
    - Ongoing
    - Blocked
    - Done
  description:
    enabled: true (default)
    type: string
  priority:
    enabled: true (default)
    type: int
    default: 3
  complexity:
    enabled: true (default)
    type: enum
    default: M
    values:
    - XS
    - S
    - M
    - L
    - XL
    - XXL
  estimate:
    enabled: false
    type: string
  checklist:
    enabled: false
    type: string[]
  story:
    enabled: false
    type: string
    default: |0
      In order to `BENEFIT`
      As a `PERSONA`
      I want to `ACTION`
  functionality:
    enabled: false
    type: string
    default: |0
      Given `STATE`
      When `TRIGGER`
      Then `OUTCOME`
  cos:
    enabled: false
    type: string[]

Taskit config has been saved!
```

> Note: you can apply an existing configuration by passing a valid yaml file

```shell
user@localhost:/home/project/repo$ taskit config ~/config/taskit.yaml
Taskit config has been saved!
```

### Task Management

A new task can be created using 3 different modes:

- Quick
- Shell
- Interactive

Using the **quick mode**, you will create a task with a title only.

```shell
user@localhost:/home/project/repo$ taskit new quick-task
New Task /1 created
```

Using the **shell mode**, you will be able to pass the the needed task information in the command.

```shell
user@localhost:/home/project/repo$ taskit new \
> --title "..." \
> --description "..." \
> --priority "..." \
> --complexity "..." \
> --tag "..."

New Task /1 created
```

Using the **interactive mode**, you will be promted with the needed task information.

```shell
user@localhost:/home/project/repo$ taskit new -i
  title: ....
  description: ....
  priority: ....
  complexity: ....
  tag: ....

New Task /1 created
```

#### Manage Tags

Tasks can have tags, it helps a lot when searching for a specific one,

Tags must be non whitespaced alphabetical characters.

To **add tags**:

```shell
user@localhost:/home/project/repo$ taskit tag /1 +mytag
Tag "mytag" added to Task /1
```

Taskit also support scopped tags, this special tag can only have one value,
setting a new tag for a scope replaces it's value.

To add **scoped tags**:

```shell
user@localhost:/home/project/repo$ taskit tag /1 +scope::value
Tag "scope::value" added to Task /1
```

To **remove tags**:

```shell
user@localhost:/home/project/repo$ taskit tag /1 -mytag
Tag "mytag" removed from Task /1
```

#### Show task information

To **show** the various information on a task:

```shell
user@localhost:/home/project/repo$ taskit show /1
status:         Open
title:          "My task title"
description:    "My task description"
priority:       3
complexity:     M
tag:            scopped::tag, other::tag
metadata:
  time-spent:   2h03m
  created:      2021/07/17 08:23:50 PM EDT
  last-update:  2021/07/17 08:43:20 PM EDT
```

To **edit** the information of a task:

```shell
user@localhost:/home/project/repo$ taskit edit /1
Task /1 has been updated
```

> Note: **edit** will use the $EDITOR environment variable to define your favorite text editor.

The **list** sub-command will display a table of the non-closed tasks,

By default the list is ordered by the last-update date.

```shell
user@localhost:/home/project/repo$ taskit list
|  ID   |        Title        |           Tag            |
| :---: | :-----------------: | :----------------------: |
|   1   |    My task title    |       scopped::tag       |
|  *2*  | My other task title | scopped::tag, other::tag |
```

> Note: stars will surround the currently active task

For **listing specific tags**, add a space-separated list of tags:

```shell
user@localhost:/home/project/repo$ taskit list --tag other::tag
|  ID   |        Title        |           Tag            |
| :---: | :-----------------: | :----------------------: |
|  *2*  | My other task title | scopped::tag, other::tag |
```

To **delete** a task:

```shell
user@localhost:/home/project/repo$ taskit delete /1
Are you sure [y/n]: y

Task /1 has been deleted
```

> Note: you can use -f or --force to skip the validation prompt

### Task Progress

#### Task Status and Time Tracking

**start** command indicates that you are starting to work on a task,

```shell
user@localhost:~$ taskit start /2
Task /2/status is now "Ongoing", /2/timer is "running"
```

**pause** command will only pause the time tracking for the currently active task.

```shell
user@localhost:~$ taskit pause
Task /2/timer is "paused"
```

> Note: Trying to `step` or `done` on a paused task will return an error.

**block** command will set the status to Blocked and pause the time tracking for the currently active task.

```shell
user@localhost:~$ taskit block
Task /2/status is now "Blocked", /2/timer is "paused"
```

**checklist** command will display the checklist for the currently active task.

```shell
user@localhost:~$ taskit step
Task /2/checklist:
| step  |  action   |
| :---: | :-------: |
|  *1*  | create it |
|   2   |  test it  |
|   3   | build it  |
|   4   |  ship it  |
```

**step** command indicates that the current step in the task checklist is complete.

```shell
user@localhost:~$ taskit step
Task /2/checklist:
| step  |  action   |
| :---: | :-------: |
|   1   | create it |
|  *2*  |  test it  |
|   3   | build it  |
|   4   |  ship it  |
```

> Note: stars will surround the currently active step in the checklist

**done** command indicates that the currently active task is complete.

```shell
user@localhost:~$ taskit done
Task /2 is complete [y/n]: y
estimate:   4h
time-spent: 3h43m

Task /2 is done.
```

> Note: you can use -f or --force to skip the validation prompt

## Changelog

For the changelogs, see [CHANGELOG](CHANGELOG.md).

## License

This Project is licensed under the MIT License see [LICENSE](./LICENSE) more details.
