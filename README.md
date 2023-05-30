# GitHub Action: amass

This action runs `amass` to scan a URL with the given parameters. Recommended use is with a @vX tag to ensure a stable version is used instead of @master. Versions can be found here [tags](https://github.com/thereisnotime/Action-amass/tags) and amass documentation for all parameters [here](https://github.com/owasp-amass/amass/blob/master/doc/user_guide.md).

## Examples

### Start a scan

```yaml
- name: Scan with amass
  uses: thereisnotime/action-amass@master
  with:
    url: "https://blackfox-security.com/"
```

### Weekly scheduled scans

```yaml
on:
  schedule:
    - cron: 0 11 * * 1 # Monday at 11 UTC

jobs:
  scan_amass:
    runs-on: ubuntu-latest
    name: SCAN | amass DAST
    steps:
      - name: Scan with amass
        uses: thereisnotime/action-amass@master
        with:
          parameters: "enum -d blackfox-security.com"
```

### Use latest version in a job

```yaml
scan_amass:
  runs-on: ubuntu-latest
  name: SCAN | amass DAST
  steps:
    - name: Scan with amass
      uses: thereisnotime/action-amass@master
      with:
        parameters: "enum -d blackfox-security.com"
```

### Use latest version in a job and upload the results back to the repository

```yaml
scan_amass:
  runs-on: ubuntu-latest
  name: SCAN | amass DAST
  steps:
    - name: Prepare environment
      run: |
        echo "START_DATE=$(date +'%d-%m-%YT%H-%M-%S-%Z')" >> $GITHUB_ENV
        echo "START_TIMESTAMP=$(date +'%s')" >> $GITHUB_ENV
        TMP_TARGET="blackfox-security.com"
        echo "TARGET=$TMP_TARGET" >> $GITHUB_ENV
    - name: Checkout
      uses: actions/checkout@v3
    - name: Scan with amass
      uses: thereisnotime/action-amass@master
      with:
        parameters: "enum -d $TARGET"
    - name: Commit and push changes
      uses: EndBug/add-and-commit@v9
      with:
        author_name: GitHub Actions
        author_email: 41898282+github-actions[bot]@users.noreply.github.com
        message: "👷 chore(amass): add report for ${{ env.TARGET }}-${{ env.START_DATE }}-${{ env.START_TIMESTAMP }}"
        add: "./reports/osint/*"
        pull: "--rebase --autostash"
```

## Inputs

### `parameters`

**Required**. Additional parameters to pass to Amass. There is no default, so you must specify this if you want to pass any arguments.

## Outputs

### `result`

JSON scan results.

### `resultb64`

JSON scan results, base64 encoded.

## Roadmap

If there is enough time or interest, I will add the following features (feel free to open PRs):

- [ ] Finish support for outputs (e.g. `result` and `resultb64`).
- [ ] Add support for notification via Slack.
- [ ] Add support for notification via Telegram.
- [ ] Add support for notification via email.
- [ ] Add support for notification via custom webhooks.
- [ ] Improve argument handling.
