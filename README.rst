=============
github-backup
=============

|PyPI| |Python Versions|

The package can be used to backup an *entire* `Github <https://github.com/>`_ organization, repository or user account, including starred repos, issues and wikis in the most appropriate format (clones for wikis, json files for issues).

Requirements
============

- GIT 1.9+
- Python

Installation
============

Using PIP via PyPI::

    pip install github-backup

Using PIP via Github (more likely the latest version)::

    pip install git+https://github.com/josegonzalez/python-github-backup.git#egg=github-backup
    
*Install note for python newcomers:*

Python scripts are unlikely to be included in your ``$PATH`` by default, this means it cannot be run directly in terminal with ``$ github-backup ...``, you can either add python's install path to your environments ``$PATH`` or call the script directly e.g. using ``$ ~/.local/bin/github-backup``.*

Basic Help
==========

Show the CLI help output::

    github-backup -h

CLI Help output::

    github-backup [-h] [-u USERNAME] [-p PASSWORD] [-t TOKEN_CLASSIC]
                  [-f TOKEN_FINE] [--as-app] [-o OUTPUT_DIRECTORY]
                  [-l LOG_LEVEL] [-i] [--starred] [--all-starred]
                  [--watched] [--followers] [--following] [--all] [--issues]
                  [--issue-comments] [--issue-events] [--pulls]
                  [--pull-comments] [--pull-commits] [--pull-details]
                  [--labels] [--hooks] [--milestones] [--repositories]
                  [--bare] [--lfs] [--wikis] [--gists] [--starred-gists]
                  [--skip-archived] [--skip-existing] [-L [LANGUAGES ...]]
                  [-N NAME_REGEX] [-H GITHUB_HOST] [-O] [-R REPOSITORY]
                  [-P] [-F] [--prefer-ssh] [-v]
                  [--keychain-name OSX_KEYCHAIN_ITEM_NAME]
                  [--keychain-account OSX_KEYCHAIN_ITEM_ACCOUNT]
                  [--releases] [--latest-releases NUMBER_OF_LATEST_RELEASES]
                  [--skip-prerelease] [--assets]
                  [--exclude [REPOSITORY [REPOSITORY ...]]
                  [--throttle-limit THROTTLE_LIMIT] [--throttle-pause THROTTLE_PAUSE]
                  USER

    Backup a github account

    positional arguments:
      USER                  github username

    optional arguments:
      -h, --help            show this help message and exit
      -u USERNAME, --username USERNAME
                            username for basic auth
      -p PASSWORD, --password PASSWORD
                            password for basic auth. If a username is given but
                            not a password, the password will be prompted for.
      -f TOKEN_FINE, --token-fine TOKEN_FINE
                            fine-grained personal access token or path to token
                            (file://...)
      -t TOKEN_CLASSIC, --token TOKEN_CLASSIC
                            personal access, OAuth, or JSON Web token, or path to
                            token (file://...)
      --as-app              authenticate as github app instead of as a user.
      -o OUTPUT_DIRECTORY, --output-directory OUTPUT_DIRECTORY
                            directory at which to backup the repositories
      -l LOG_LEVEL, --log-level LOG_LEVEL
                            log level to use (default: info, possible levels:
                            debug, info, warning, error, critical)
      -i, --incremental     incremental backup
      --incremental-by-files incremental backup using modified time of files
      --starred             include JSON output of starred repositories in backup
      --all-starred         include starred repositories in backup [*]
      --watched             include JSON output of watched repositories in backup
      --followers           include JSON output of followers in backup
      --following           include JSON output of following users in backup
      --all                 include everything in backup (not including [*])
      --issues              include issues in backup
      --issue-comments      include issue comments in backup
      --issue-events        include issue events in backup
      --pulls               include pull requests in backup
      --pull-comments       include pull request review comments in backup
      --pull-commits        include pull request commits in backup
      --pull-details        include more pull request details in backup [*]
      --labels              include labels in backup
      --hooks               include hooks in backup (works only when
                            authenticated)
      --milestones          include milestones in backup
      --repositories        include repository clone in backup
      --bare                clone bare repositories
      --lfs                 clone LFS repositories (requires Git LFS to be
                            installed, https://git-lfs.github.com) [*]
      --wikis               include wiki clone in backup
      --gists               include gists in backup [*]
      --starred-gists       include starred gists in backup [*]
      --skip-existing       skip project if a backup directory exists
      -L [LANGUAGES [LANGUAGES ...]], --languages [LANGUAGES [LANGUAGES ...]]
                            only allow these languages
      -N NAME_REGEX, --name-regex NAME_REGEX
                            python regex to match names against
      -H GITHUB_HOST, --github-host GITHUB_HOST
                            GitHub Enterprise hostname
      -O, --organization    whether or not this is an organization user
      -R REPOSITORY, --repository REPOSITORY
                            name of repository to limit backup to
      -P, --private         include private repositories [*]
      -F, --fork            include forked repositories [*]
      --prefer-ssh          Clone repositories using SSH instead of HTTPS
      -v, --version         show program's version number and exit
      --keychain-name OSX_KEYCHAIN_ITEM_NAME
                            OSX ONLY: name field of password item in OSX keychain
                            that holds the personal access or OAuth token
      --keychain-account OSX_KEYCHAIN_ITEM_ACCOUNT
                            OSX ONLY: account field of password item in OSX
                            keychain that holds the personal access or OAuth token
      --releases            include release information, not including assets or
                            binaries
      --latest-releases NUMBER_OF_LATEST_RELEASES
                            include certain number of the latest releases;
                            only applies if including releases
      --skip-prerelease     skip prerelease and draft versions; only applies if including releases
      --assets              include assets alongside release information; only
                            applies if including releases
      --exclude [REPOSITORY [REPOSITORY ...]]
                            names of repositories to exclude from backup.
      --throttle-limit THROTTLE_LIMIT
                            start throttling of GitHub API requests after this
                            amount of API requests remain
      --throttle-pause THROTTLE_PAUSE
                            wait this amount of seconds when API request
                            throttling is active (default: 30.0, requires
                            --throttle-limit to be set)


Usage Details
=============

Authentication
--------------

**Password-based authentication** will fail if you have two-factor authentication enabled, and will `be deprecated <https://github.blog/2023-03-09-raising-the-bar-for-software-security-github-2fa-begins-march-13/>`_ by 2023 EOY.

``--username`` is used for basic password authentication and separate from the positional argument ``USER``, which specifies the user account you wish to back up.

**Classic tokens** are `slightly less secure <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#personal-access-tokens-classic>`_ as they provide very coarse-grained permissions.

If you need authentication for long-running backups (e.g. for a cron job) it is recommended to use **fine-grained personal access token** ``-f TOKEN_FINE``.


Fine Tokens
~~~~~~~~~~~

You can "generate new token", choosing the repository scope by selecting specific repos or all repos. On Github this is under *Settings -> Developer Settings -> Personal access tokens -> Fine-grained Tokens*

Customise the permissions for your use case, but for a personal account full backup you'll need to enable the following permissions:

**User permissions**: Read access to followers, starring, and watching.

**Repository permissions**: Read access to contents, issues, metadata, pull requests, and webhooks.


GitHub App Authentication
~~~~~~~~~~~~~~~~~~~~~~~~~~

For backing up entire organizations, **GitHub App authentication** (``--as-app``) is often the most effective approach as it provides broader access across organization repositories and higher rate limits.

Creating a GitHub App for Organization Backup
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. **Create the GitHub App**:
   
   * Go to your organization's settings: ``https://github.com/organizations/YOUR_ORG/settings/apps``
   * Click "New GitHub App"
   * Fill in basic information:
     - App name: e.g., "Organization Backup Tool" 
     - Homepage URL: Can be your organization's website
     - Webhook URL: Not required, can leave blank or use a placeholder

2. **Configure Permissions**:

   **Repository permissions** (select "Read" access for):
   
   * Contents
   * Issues  
   * Metadata
   * Pull requests
   * Webhooks
   * Repository projects (if backing up projects)

   **Organization permissions** (select "Read" access for):
   
   * Members
   * Metadata

   **Account permissions** (select "Read" access for):
   
   * Starring
   * Watching

3. **Installation Settings**:
   
   * Set "Where can this GitHub App be installed?" to "Only on this account" for security
   * Under "Repository access", choose "All repositories" to backup the entire organization

4. **Generate Keys and Secret**:
   
   * After creating the app, go to "General" tab and scroll down to "Private keys"
   * Click "Generate a private key" and download the ``.pem`` file safely
   * Note your **App ID** (displayed at the top of the General tab)
   * Click "Generate a new client secret" and copy the client secret (you'll need this for automated scripts)

5. **Install the App**:
   
   * Go to "Install App" tab in your app settings
   * Click "Install" next to your organization
   * Choose "All repositories" or select specific repositories you want to backup
   * Note the **Installation ID** from the URL after installation (e.g., ``https://github.com/organizations/ORG/settings/installations/12345678`` - the installation ID is ``12345678``)

Generating Installation Access Tokens for Automated Backups
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GitHub Apps use installation access tokens that expire after 1 hour. For automated backups (e.g., cron jobs), you need to generate these tokens programmatically using your app's credentials.

**Complete Script for Token Generation**:

Create a script (e.g., ``generate-github-token.py``) to generate installation access tokens::

    #!/usr/bin/env python3
    import jwt
    import time
    import requests
    import os
    import sys
    
    # Your GitHub App details - set these as environment variables or modify here
    APP_ID = os.environ.get('GITHUB_APP_ID', 'YOUR_APP_ID')
    PRIVATE_KEY_PATH = os.environ.get('GITHUB_PRIVATE_KEY_PATH', '/path/to/your/private-key.pem')
    INSTALLATION_ID = os.environ.get('GITHUB_INSTALLATION_ID', 'YOUR_INSTALLATION_ID')
    
    def generate_installation_token():
        # Read the private key
        try:
            with open(PRIVATE_KEY_PATH, 'r') as key_file:
                private_key = key_file.read()
        except FileNotFoundError:
            print(f"Error: Private key file not found at {PRIVATE_KEY_PATH}")
            sys.exit(1)
        
        # Generate JWT token
        now = int(time.time())
        payload = {
            'iat': now - 60,  # Issued 1 minute in the past to avoid clock drift
            'exp': now + 600,  # Expires in 10 minutes
            'iss': APP_ID
        }
        
        try:
            jwt_token = jwt.encode(payload, private_key, algorithm='RS256')
        except Exception as e:
            print(f"Error generating JWT: {e}")
            sys.exit(1)
        
        # Get installation access token
        headers = {
            'Authorization': f'Bearer {jwt_token}',
            'Accept': 'application/vnd.github.v3+json',
            'X-GitHub-Api-Version': '2022-11-28'
        }
        
        try:
            response = requests.post(
                f'https://api.github.com/app/installations/{INSTALLATION_ID}/access_tokens',
                headers=headers
            )
            response.raise_for_status()
            return response.json()['token']
        except requests.exceptions.RequestException as e:
            print(f"Error getting installation token: {e}")
            if response.status_code == 404:
                print("Check your installation ID - the app may not be installed or ID is incorrect")
            sys.exit(1)
    
    if __name__ == '__main__':
        token = generate_installation_token()
        print(token)

**Setup for Automated Cron Jobs**:

1. **Install required Python packages**::

    pip install PyJWT requests

2. **Set up environment variables** (in your cron environment or script)::

    export GITHUB_APP_ID="123456"
    export GITHUB_PRIVATE_KEY_PATH="/secure/path/to/github-app-private-key.pem"
    export GITHUB_INSTALLATION_ID="12345678"

3. **Create a backup script** (e.g., ``nightly-backup.sh``)::

    #!/bin/bash
    set -e
    
    # Generate fresh GitHub App installation token
    GITHUB_APP_TOKEN=$(python3 /path/to/generate-github-token.py)
    
    if [ -z "$GITHUB_APP_TOKEN" ]; then
        echo "Failed to generate GitHub App token"
        exit 1
    fi
    
    # Run the backup
    github-backup YOUR_ORGANIZATION \
        --token "$GITHUB_APP_TOKEN" \
        --as-app \
        --organization \
        --output-directory /backup/github-org \
        --incremental \
        --private \
        --repositories \
        --wikis \
        --issues \
        --pulls \
        --issue-comments \
        --pull-comments \
        --labels \
        --milestones \
        --log-level error

4. **Add to crontab for nightly runs**::

    # Edit crontab
    crontab -e
    
    # Add this line for nightly backup at 2 AM
    0 2 * * * /path/to/nightly-backup.sh >> /var/log/github-backup.log 2>&1

**Finding Your App Credentials**:

* **App ID**: Found in your GitHub App settings under "General" tab (top of page)
* **Installation ID**: Found in the URL after installing the app: ``https://github.com/organizations/YOUR_ORG/settings/installations/INSTALLATION_ID``
* **Private Key**: Downloaded as ``.pem`` file when you generate it in app settings
* **Client Secret**: Generated in app settings (not needed for this token generation method)

Using GitHub App for Organization Backup
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have an installation access token, use it with the ``--as-app`` flag::

    # Full organization backup with GitHub App
    export GITHUB_APP_TOKEN="ghs_xxxxxxxxxxxxxxxxxxxx"
    github-backup YOUR_ORG \
        --token $GITHUB_APP_TOKEN \
        --as-app \
        --organization \
        --output-directory /backup/github-org \
        --all \
        --private \
        --repositories \
        --wikis \
        --issues \
        --pulls

**Key differences when using** ``--as-app``:

* Higher rate limits (5000 requests/hour per installation)
* Access to all organization repositories (if app is installed with "All repositories")
* Uses ``Authorization: token <installation_token>`` header format
* Includes GitHub App API headers for proper app identification
* Works with organization-wide permissions

**Important Notes**:

* Installation access tokens expire after 1 hour - you may need to refresh them for long-running backups
* The app must be installed on the organization with appropriate repository access
* Use classic personal access tokens (``-t TOKEN_CLASSIC``) with ``--as-app``, not fine-grained tokens
* GitHub Apps have separate rate limits from personal access tokens

Token Type Selection: Classic vs Fine-Grained
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Why Classic Tokens with GitHub Apps?**

When using ``--as-app``, you must use classic personal access tokens (``-t TOKEN_CLASSIC``) rather than fine-grained tokens (``-f TOKEN_FINE``) for the following technical reasons:

1. **GitHub App Installation Tokens are Classic Format**: Installation access tokens generated by GitHub Apps follow the classic token format (``ghs_`` prefix), not the fine-grained format
2. **API Compatibility**: The ``--as-app`` flag configures the tool to use GitHub App-specific API headers and authentication methods that expect classic token format
3. **Scope Differences**: Fine-grained tokens are designed for user-scoped access to specific repositories, while GitHub App installation tokens provide organization-wide access with app-specific permissions

**Fine-grained tokens** are intended for:
- User personal access with repository-specific scopes
- Direct user authentication (not app authentication)
- Newer, more granular permission model

**Classic tokens** (including GitHub App installation tokens) are used for:
- Application-based authentication (``--as-app``)
- Organization-wide access patterns
- Legacy API compatibility requirements

Handling Long-Running Backups and Token Expiry
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**The 1-Hour Token Expiry Challenge**

GitHub App installation access tokens expire after exactly 1 hour. For large organization backups that may take several hours, this creates a potential problem:

* **What happens during expiry**: When the token expires mid-backup, GitHub API requests will start returning ``401 Unauthorized`` errors
* **Impact on backup**: The backup process will fail and exit, potentially leaving an incomplete backup
* **Data integrity**: Depending on when the expiry occurs, you may have partial repository clones, incomplete issue data, or missing metadata

**Strategies for Long-Running Backups**

**1. Pre-emptive Token Refresh Strategy**

Create a wrapper script that monitors backup duration and refreshes tokens proactively::

    #!/bin/bash
    # long-running-backup.sh
    set -e
    
    ORGANIZATION="$1"
    BACKUP_DIR="$2"
    GITHUB_APP_TOKEN=""
    BACKUP_PID=""
    
    # Function to generate fresh token
    generate_token() {
        echo "Generating fresh GitHub App token..."
        GITHUB_APP_TOKEN=$(python3 /path/to/generate-github-token.py)
        if [ -z "$GITHUB_APP_TOKEN" ]; then
            echo "Failed to generate token"
            exit 1
        fi
        echo "Token generated successfully"
    }
    
    # Function to start backup in background
    start_backup() {
        echo "Starting backup process..."
        github-backup "$ORGANIZATION" \
            --token "$GITHUB_APP_TOKEN" \
            --as-app \
            --organization \
            --output-directory "$BACKUP_DIR" \
            --incremental \
            --private \
            --repositories \
            --wikis \
            --issues \
            --pulls \
            --issue-comments \
            --pull-comments \
            --labels \
            --milestones \
            --log-level info &
        BACKUP_PID=$!
        echo "Backup started with PID: $BACKUP_PID"
    }
    
    # Main backup loop with token refresh
    run_backup_with_refresh() {
        generate_token
        start_backup
        
        # Monitor backup and refresh token every 50 minutes (before 1-hour expiry)
        while kill -0 $BACKUP_PID 2>/dev/null; do
            echo "Backup running... waiting 50 minutes before token refresh"
            sleep 3000  # 50 minutes
            
            if kill -0 $BACKUP_PID 2>/dev/null; then
                echo "Backup still running, killing to refresh token..."
                kill $BACKUP_PID
                wait $BACKUP_PID 2>/dev/null || true
                
                # Generate new token and restart
                generate_token
                start_backup
            fi
        done
        
        wait $BACKUP_PID
        echo "Backup completed successfully"
    }
    
    # Usage: ./long-running-backup.sh myorg /backup/path
    run_backup_with_refresh

**2. Segmented Backup Strategy**

Break large backups into smaller chunks that complete within the token lifetime::

    #!/bin/bash
    # segmented-backup.sh
    set -e
    
    ORGANIZATION="$1"
    BACKUP_DIR="$2"
    
    # Generate fresh token for each segment
    generate_token() {
        python3 /path/to/generate-github-token.py
    }
    
    # Backup repositories only (usually the longest part)
    echo "=== Backing up repositories ==="
    GITHUB_APP_TOKEN=$(generate_token)
    github-backup "$ORGANIZATION" \
        --token "$GITHUB_APP_TOKEN" \
        --as-app \
        --organization \
        --output-directory "$BACKUP_DIR" \
        --incremental \
        --private \
        --repositories \
        --wikis
    
    # Backup issues and pull requests
    echo "=== Backing up issues and pulls ==="
    GITHUB_APP_TOKEN=$(generate_token)
    github-backup "$ORGANIZATION" \
        --token "$GITHUB_APP_TOKEN" \
        --as-app \
        --organization \
        --output-directory "$BACKUP_DIR" \
        --incremental \
        --issues \
        --pulls \
        --issue-comments \
        --pull-comments
    
    # Backup metadata
    echo "=== Backing up metadata ==="
    GITHUB_APP_TOKEN=$(generate_token)
    github-backup "$ORGANIZATION" \
        --token "$GITHUB_APP_TOKEN" \
        --as-app \
        --organization \
        --output-directory "$BACKUP_DIR" \
        --incremental \
        --labels \
        --milestones
    
    echo "Segmented backup completed"

**3. Error-Resilient Incremental Strategy**

Use incremental backups with error handling to resume from failures::

    #!/bin/bash
    # resilient-backup.sh
    set -e
    
    ORGANIZATION="$1"
    BACKUP_DIR="$2"
    MAX_RETRIES=3
    
    run_backup_with_retry() {
        local attempt=1
        
        while [ $attempt -le $MAX_RETRIES ]; do
            echo "Backup attempt $attempt of $MAX_RETRIES"
            
            # Generate fresh token for each attempt
            GITHUB_APP_TOKEN=$(python3 /path/to/generate-github-token.py)
            
            if github-backup "$ORGANIZATION" \
                --token "$GITHUB_APP_TOKEN" \
                --as-app \
                --organization \
                --output-directory "$BACKUP_DIR" \
                --incremental \
                --private \
                --repositories \
                --wikis \
                --issues \
                --pulls \
                --issue-comments \
                --pull-comments \
                --labels \
                --milestones \
                --log-level info; then
                echo "Backup completed successfully on attempt $attempt"
                return 0
            else
                echo "Backup failed on attempt $attempt"
                if [ $attempt -eq $MAX_RETRIES ]; then
                    echo "All retry attempts exhausted"
                    return 1
                fi
                attempt=$((attempt + 1))
                echo "Waiting 2 minutes before retry..."
                sleep 120
            fi
        done
    }
    
    run_backup_with_retry

**Recommended Approach for Production**

For automated nightly backups, the **segmented backup strategy** is recommended because:

1. **Predictable timing**: Each segment completes well within 1 hour
2. **Clear progress**: You can see which parts completed successfully
3. **Efficient recovery**: If one segment fails, you don't need to restart everything
4. **Resource friendly**: Uses incremental backups to minimize repeated work

**Monitoring Token Expiry**

To detect token expiry issues in your logs, watch for these error patterns::

    # In your backup logs, look for:
    grep -i "401\|unauthorized\|token.*expired\|authentication.*failed" /var/log/github-backup.log

Set up alerting on these patterns to get notified when token refresh is needed.


Prefer SSH
~~~~~~~~~~

If cloning repos is enabled with ``--repositories``, ``--all-starred``, ``--wikis``, ``--gists``, ``--starred-gists`` using the ``--prefer-ssh`` argument will use ssh for cloning the git repos, but all other connections will still use their own protocol, e.g. API requests for issues uses HTTPS.

To clone with SSH, you'll need SSH authentication setup `as usual with Github <https://docs.github.com/en/authentication/connecting-to-github-with-ssh>`_, e.g. via SSH public and private keys.


Using the Keychain on Mac OSX
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Note: On Mac OSX the token can be stored securely in the user's keychain. To do this:

1. Open Keychain from "Applications -> Utilities -> Keychain Access"
2. Add a new password item using "File -> New Password Item"
3. Enter a name in the "Keychain Item Name" box. You must provide this name to github-backup using the --keychain-name argument.
4. Enter an account name in the "Account Name" box, enter your Github username as set above. You must provide this name to github-backup using the --keychain-account argument.
5. Enter your Github personal access token in the "Password" box

Note:  When you run github-backup, you will be asked whether you want to allow "security" to use your confidential information stored in your keychain. You have two options:

1. **Allow:** In this case you will need to click "Allow" each time you run `github-backup`
2. **Always Allow:** In this case, you will not be asked for permission when you run `github-backup` in future. This is less secure, but is required if you want to schedule `github-backup` to run automatically


Github Rate-limit and Throttling
--------------------------------

"github-backup" will automatically throttle itself based on feedback from the Github API. 

Their API is usually rate-limited to 5000 calls per hour. The API will ask github-backup to pause until a specific time when the limit is reset again (at the start of the next hour). This continues until the backup is complete.

During a large backup, such as ``--all-starred``, and on a fast connection this can result in (~20 min) pauses with bursts of API calls periodically maxing out the API limit. If this is not suitable `it has been observed <https://github.com/josegonzalez/python-github-backup/issues/76#issuecomment-636158717>`_ under real-world conditions that overriding the throttle with ``--throttle-limit 5000 --throttle-pause 0.6`` provides a smooth rate across the hour, although a ``--throttle-pause 0.72`` (3600 seconds [1 hour] / 5000 limit) is theoretically safer to prevent large rate-limit pauses.


About Git LFS
-------------

When you use the ``--lfs`` option, you will need to make sure you have Git LFS installed.

Instructions on how to do this can be found on https://git-lfs.github.com.


Run in Docker container
-----------------------

To run the tool in a Docker container use the following command:

    sudo docker run --rm -v /path/to/backup:/data --name github-backup ghcr.io/josegonzalez/python-github-backup -o /data $OPTIONS $USER

Gotchas / Known-issues
======================

All is not everything
---------------------

The ``--all`` argument does not include: cloning private repos (``-P, --private``), cloning forks (``-F, --fork``), cloning starred repositories (``--all-starred``), ``--pull-details``, cloning LFS repositories (``--lfs``), cloning gists (``--gists``) or cloning starred gist repos (``--starred-gists``). See examples for more.

Cloning all starred size
------------------------

Using the ``--all-starred`` argument to clone all starred repositories may use a large amount of storage space, especially if ``--all`` or more arguments are used. e.g. commonly starred repos can have tens of thousands of issues, many large assets and the repo itself etc. Consider just storing links to starred repos in JSON format with ``--starred``.

Incremental Backup
------------------

Using (``-i, --incremental``) will only request new data from the API **since the last run (successful or not)**. e.g. only request issues from the API since the last run. 

This means any blocking errors on previous runs can cause a large amount of missing data in backups.

Using (``--incremental-by-files``) will request new data from the API **based on when the file was modified on filesystem**. e.g. if you modify the file yourself you may miss something.

Still saver than the previous version.

Specifically, issues and pull requests are handled like this.

Known blocking errors
---------------------

Some errors will block the backup run by exiting the script. e.g. receiving a 403 Forbidden error from the Github API.

If the incremental argument is used, this will result in the next backup only requesting API data since the last blocked/failed run. Potentially causing unexpected large amounts of missing data.

It's therefore recommended to only use the incremental argument if the output/result is being actively monitored, or complimented with periodic full non-incremental runs, to avoid unexpected missing data in a regular backup runs.

1. **Starred public repo hooks blocking**

   Since the ``--all`` argument includes ``--hooks``, if you use ``--all`` and ``--all-starred`` together to clone a users starred public repositories, the backup will likely error and block the backup continuing. 

   This is due to needing the correct permission for ``--hooks`` on public repos.


"bare" is actually "mirror"
---------------------------

Using the bare clone argument (``--bare``) will actually call git's ``clone --mirror`` command. There's a subtle difference between `bare <https://www.git-scm.com/docs/git-clone#Documentation/git-clone.txt---bare>`_ and `mirror <https://www.git-scm.com/docs/git-clone#Documentation/git-clone.txt---mirror>`_ clone.

*From git docs "Compared to --bare, --mirror not only maps local branches of the source to local branches of the target, it maps all refs (including remote-tracking branches, notes etc.) and sets up a refspec configuration such that all these refs are overwritten by a git remote update in the target repository."*


Starred gists vs starred repo behaviour
---------------------------------------

The starred normal repo cloning (``--all-starred``) argument stores starred repos separately to the users own repositories. However, using ``--starred-gists`` will store starred gists within the same directory as the users own gists ``--gists``. Also, all gist repo directory names are IDs not the gist's name.


Skip existing on incomplete backups
-----------------------------------

The ``--skip-existing`` argument will skip a backup if the directory already exists, even if the backup in that directory failed (perhaps due to a blocking error). This may result in unexpected missing data in a regular backup.


Github Backup Examples
======================

Backup all repositories, including private ones using a classic token::

    export ACCESS_TOKEN=SOME-GITHUB-TOKEN
    github-backup WhiteHouse --token $ACCESS_TOKEN --organization --output-directory /tmp/white-house --repositories --private

Use a fine-grained access token to backup a single organization repository with everything else (wiki, pull requests, comments, issues etc)::

    export FINE_ACCESS_TOKEN=SOME-GITHUB-TOKEN
    ORGANIZATION=docker
    REPO=cli
    # e.g. git@github.com:docker/cli.git
    github-backup $ORGANIZATION -P -f $FINE_ACCESS_TOKEN -o . --all -O -R $REPO

Quietly and incrementally backup useful Github user data (public and private repos with SSH) including; all issues, pulls, all public starred repos and gists (omitting "hooks", "releases" and therefore "assets" to prevent blocking). *Great for a cron job.* ::

    export FINE_ACCESS_TOKEN=SOME-GITHUB-TOKEN
    GH_USER=YOUR-GITHUB-USER

    github-backup -f $FINE_ACCESS_TOKEN --prefer-ssh -o ~/github-backup/ -l error -P -i --all-starred --starred --watched --followers --following --issues --issue-comments --issue-events --pulls --pull-comments --pull-commits --labels --milestones --repositories --wikis --releases --assets --pull-details --gists --starred-gists $GH_USER
    
Debug an error/block or incomplete backup into a temporary directory. Omit "incremental" to fill a previous incomplete backup. ::

    export FINE_ACCESS_TOKEN=SOME-GITHUB-TOKEN
    GH_USER=YOUR-GITHUB-USER

    github-backup -f $FINE_ACCESS_TOKEN -o /tmp/github-backup/ -l debug -P --all-starred --starred --watched --followers --following --issues --issue-comments --issue-events --pulls --pull-comments --pull-commits --labels --milestones --repositories --wikis --releases --assets --pull-details --gists --starred-gists $GH_USER


GitHub App Organization Backup Examples
========================================

Backup entire organization using GitHub App (recommended for organizations)::

    export GITHUB_APP_TOKEN=ghs_xxxxxxxxxxxxxxxxxxxx  # Installation access token
    ORGANIZATION=mycompany
    
    github-backup $ORGANIZATION \
        --token $GITHUB_APP_TOKEN \
        --as-app \
        --organization \
        --output-directory /backup/github-org \
        --all \
        --private \
        --repositories \
        --wikis \
        --issues \
        --pulls \
        --issue-comments \
        --pull-comments \
        --labels \
        --milestones

Incremental organization backup with GitHub App for automated/cron scenarios::

    export GITHUB_APP_TOKEN=ghs_xxxxxxxxxxxxxxxxxxxx
    ORGANIZATION=mycompany
    
    github-backup $ORGANIZATION \
        --token $GITHUB_APP_TOKEN \
        --as-app \
        --organization \
        --output-directory /backup/github-org \
        --incremental \
        --private \
        --repositories \
        --wikis \
        --issues \
        --pulls \
        --issue-comments \
        --pull-comments \
        --labels \
        --milestones \
        --log-level error

Backup specific organization repository with comprehensive data using GitHub App::

    export GITHUB_APP_TOKEN=ghs_xxxxxxxxxxxxxxxxxxxx
    ORGANIZATION=mycompany
    REPO=main-project
    
    github-backup $ORGANIZATION \
        --token $GITHUB_APP_TOKEN \
        --as-app \
        --organization \
        --repository $REPO \
        --output-directory /backup/specific-repo \
        --all \
        --private \
        --pull-details \
        --releases \
        --assets

Organization backup excluding certain repositories::

    export GITHUB_APP_TOKEN=ghs_xxxxxxxxxxxxxxxxxxxx
    ORGANIZATION=mycompany
    
    github-backup $ORGANIZATION \
        --token $GITHUB_APP_TOKEN \
        --as-app \
        --organization \
        --output-directory /backup/github-org \
        --all \
        --private \
        --exclude repo-to-skip another-repo-to-skip \
        --throttle-limit 4500 \
        --throttle-pause 0.8


Development
===========

This project is considered feature complete for the primary maintainer @josegonzalez. If you would like a bugfix or enhancement, pull requests are welcome. Feel free to contact the maintainer for consulting estimates if you'd like to sponsor the work instead.

Contibuters
-----------

A huge thanks to all the contibuters!

.. image:: https://contrib.rocks/image?repo=josegonzalez/python-github-backup
   :target: https://github.com/josegonzalez/python-github-backup/graphs/contributors
   :alt: contributors

Testing
-------

This project currently contains no unit tests.  To run linting::

    pip install flake8
    flake8 --ignore=E501


.. |PyPI| image:: https://img.shields.io/pypi/v/github-backup.svg
   :target: https://pypi.python.org/pypi/github-backup/
.. |Python Versions| image:: https://img.shields.io/pypi/pyversions/github-backup.svg
   :target: https://github.com/josegonzalez/python-github-backup
