# GitHub Actions job_id parser

GitHub Action to get the current workflow run's job_id

This GitHub Action depends on the GitHub REST API v3.

https://developer.github.com/v3/actions/workflow_jobs/

## Inputs

### `github_token`

**Required** GITHUB_TOKEN to use GitHub API v3

Simply, just specify `${{ secrets.GITHUB_TOKEN }}`.

### `job_name`

**Required** `jobs.<job-id>.name` of tartget workflow jobs

If no name specified, use `jobs.<job-id>` instead. Note that `<job-id>` != job_id.  
If you are using this actions with matrix workflow, check the example section.


```yaml
jobs:
  my_first_job:         # this is jobs.<job-id>
    name: My first job  # this is jobs.<job-id>.name
  my_second_job:        # this is jobs.<job-id>
    name: My second job # this is jobs.<job-id>.name
```

* `jobs.<job-id>`: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id
* `jobs.<job-id>.name`: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idname

### `repository`

target GitHub repository

default: `${GITHUB_REPOSITORY}`

### `run_id`

run_id of target workflow run

default: `${GITHUB_RUN_ID}`

###  `per_page`

Results per page (max 100) of target workflow run

default: 30

https://docs.github.com/en/rest/reference/actions#list-jobs-for-a-workflow-run-attempt

If there are more than 30 jobs for the target workflow, set this parameter.

## Outputs

### `job_id`

job_id of target workflow jobs

### `html_url`

html_url of target workflow jobs

## Permissions

The job using this GitHub Actions must have the `actions:read` permission.

## Example usage

### Simple example 1

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v3
      - name: Some Scripts
        run: echo "do something here"
      - name: Get Current Job Log URL
        uses: Tiryoh/gha-jobid-action@v0
        id: jobs
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          job_name: "build" # input job.<job-id>
          #job_name: "${{ github.job }}"  # if job.<job-id>.name is not specified, this works too
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

### Simple example 2

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    name: Build and Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v3
      - name: Some Scripts
        run: echo "do something here"
      - name: Get Current Job Log URL
        uses: Tiryoh/gha-jobid-action@v0
        id: jobs
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          job_name: "Build and Test" # input job.<job-id>.name here. job.<job-id> won't works.
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

### Matrix example

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    strategy:
      matrix:
        distro: [alpha, beta, gamma]
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v3
      - name: Some Scripts
        run: echo "do something here ${{ matrix.distro }}"
      - name: Get Current Job Log URL
        uses: Tiryoh/gha-jobid-action@v0
        id: jobs
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          job_name: "build (${{ matrix.distro }})" # input job.<job-id>.name and matrix here.
          per_page: 50 # input matrix size here if it is larger than 30
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

* Get current `job_id` URL
  * https://github.com/Tiryoh/docker-ros2-desktop-vnc/blob/ad3b893722b3f56c3e772e5f43efb2eb1bf682fb/.github/workflows/deploy.yml#L64-L69
* Output `job_id` URL
  * https://github.com/Tiryoh/docker-ros2-desktop-vnc/blob/ad3b893722b3f56c3e772e5f43efb2eb1bf682fb/.github/workflows/deploy.yml#L88

## Contributors

Special Thanks

* [@IngoStrauch2020](https://github.com/IngoStrauch2020)
  * [Issue#1](https://github.com/Tiryoh/gha-jobid-action/issues/1)
* Petr Pučil ([@generalmimon](https://github.com/generalmimon))
  * [Pull Request#3](https://github.com/Tiryoh/gha-jobid-action/pull/3)
* Masaya Suzuki ([@massongit](https://github.com/massongit))
  * [Pull Request#12](https://github.com/Tiryoh/gha-jobid-action/pull/12)
* [phoeniixdotcom](https://github.com/phoeniixdotcom)
  * [Pull Request#17](https://github.com/Tiryoh/gha-jobid-action/pull/17)

Contributions are always welcome!

## License

Copyright (c) 2020-2023 Daisuke Sato

This repository is licensed under the MIT License, see [LICENSE](./LICENSE).  
Unless attributed otherwise, everything in this repository is licensed under the MIT license.
