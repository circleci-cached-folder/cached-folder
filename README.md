# CircleCI: Cached Folder

This is a Circle CI orb (aka library) for caching a whole folder of generated code or files.

A this is to speed up the CI/CD workflow to enable faster builds.

To use this simply copy the `commands` block from `config.yml` into your `.circleci/config.yml` file and call it like so:

```yaml

jobs:

  # Workflow step to create the `gen` folder:
  proto:
    machine:
      image: ubuntu-2004:202101-01
      docker_layer_caching: true

    working_directory: ~/repo

    steps:
      - checkout

      - run:
          name: Download deps
          command: echo "add setup steps here..."

      - cached-folder:
          lock_file: proto.lock
          input_folder: proto
          output_folder: gen
          generate_command: make proto

      - persist_to_workspace:
          root: .
          paths:
            - gen

  # Use the gen folder in your unit-tests and linters
  golint:
    working_directory: ~/repo
    docker:
      - image: cimg/go:1.16.5
    steps:
      - checkout

      - attach_workspace:
          at: .

      #- ...
```
