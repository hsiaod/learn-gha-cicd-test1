# CorpX Documentation

Welcome to CorpX documentation! Our documentation serves as a vital resource for understanding our products, services, and processes. It ensures clarity, consistency, and accessibility for both internal teams and external users. We value your contributions to keeping our documentation robust and up-to-date.

## Why Documentation Matters

Clear and comprehensive documentation is essential for several reasons:

- **Accessibility**: It provides easy access to information for all stakeholders, including developers, designers, and end-users.
- **Consistency**: It ensures consistency in communication, reducing confusion and errors.
- **Onboarding**: New team members can quickly familiarize themselves with our products and processes, accelerating their onboarding process.
- **Troubleshooting**: It serves as a reference point for troubleshooting common issues and resolving queries efficiently.

## Your task

You are a new DevOps engineer at CorpX. You have been tasked with creating a GitHub Actions workflow to automate the deployment process of CorpX's internal knowledge base.

This knowledge base receives contributions on a daily basis through pull requests to this repository. The previous engineers have been releasing the changes manually at the end of each week. However, this is not good enough for CorpX. They want to publish changes as soon as they are merged to the main branch.

## Setup

### Test your environment

1. Launch your project workspace and open the terminal (Use the menu bar at the top: Terminal > New Terminal)

1. Run the web server and make sure the docs are rendered, with images and style properly:

    ```bash
    $ npm run workspace:dev

    ...
    Starting up http-server, serving ./

    http-server version: 14.1.1
    
    http-server settings: 
    CORS: disabled
    Cache: 3600 seconds
    Connection Timeout: 120 seconds
    Directory Listings: visible
    AutoIndex: visible
    Serve GZIP Files: false
    Serve Brotli Files: false
    Default File Extension: none
    
    Available on:
      http://127.0.0.1:8080
      http://10.124.12.199:8080
    Hit CTRL-C to stop the server
    Open: http://127.0.0.1:8080/_site/

    ```

1. If everything went well, you will see: `Open: http://127.0.0.1:8080/_site/`

1. To open the site, you need to navigate to the "Ports" tab (right next to the terminal tab)

1. Under "Local Address" you should see a URL similar to this: `https://yotzaustpu.prod.udacity-student-workspaces.com/proxy/8080/`

1. Click on the "Globe" icon to open the link directly in your browser or copy and paste the full link
    1. You should be able to see the knowledge base fully rendered and you should be able to navigate through all the links

Once the above is done, you can proceed to the next steps.

## Requirements

Create a new GitHub Actions workflow called `deploy.yaml`. This workflow should at least:

1. Trigger a workflow run when changes are pushed to the `main` branch and only the `main` branch. All pushes to other branches should be ignored
    1. Do no trigger a run when any of these files have been changed:
        1. `.gitignore`
        2. `README.md`
        3. `CONTRIBUTING.md`
        4. `LICENSE`
2. Trigger a workflow run when for every `pull_request` created again the `main` branch only. All pull requests created against other branches should be ignored
3. In case of an emergency, you'll want the option to trigger a force deployment. Your workflow should include that option
4. Your workflow should have a maximum of 1 run at all times. If other runs are queued, the older runs should be canceled
5. Your workflow should have at least 3 jobs:
    1. `test`
    2. `build`
    3. `deploy`
6. Your workflow should run fast, it should not take more than `2 minutes (120 seconds)` (on average) to run all the jobs
7. You must adopt the security best practices discussed in the course and [outlined in the docs](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)

### `test` job

For this job, you are required to:

1. Checkout the repository
2. Setup Node (version 18+)
3. Install all the dependencies needed. Use the cache to speed up the process
4. Run all the linters
5. Run all the scripts in the folder `script/**`
6. You should define and use a repository variable named `URL_CHECKER_TIMEOUT` to configure the `timeout` value for the `script/url-checker.js` script
    1. Use any reasonable value (in seconds)

The job should fail if any of its steps fail.

### `build` job

For this job, you are required to:

1. Make sure that the `test` job ran and succeeded
2. Checkout the repository
3. Setup pages using `actions/configure-pages@v4`
4. Setup Node (version 18+) 
5. Install all the dependencies needed. Use the cache to speed up the process
6. Build the static pages
    1. In order for 11ty to build your site successfully, it requires an environment variable `PATH_PREFIX` to be set and to have the value of the name of your repository
    2. The `PATH_PREFIX` should be of the following format: `/repository name/`. Make sure that the repository name does not include the repository owner! Do not forget the `/` at the start and end
7. Build your site by running: `npm run prod`
8. Upload all the content of the built `_site` as an artifact

The job should fail if any of its steps fail.

### `deploy` job

For this job, you are required to:

1. Never deploy changes if any of the previous jobs (`test`, `build`) fail
2. Deploy your site to GitHub pages
3. Using the GitHub CLI, authenticate to GitHub with `GITHUB_TOKEN` and create an issue that reports the status of the deployment. The issue should have:
    1. Title: `<date> - Deployment: <status>`
        1. Make sure to replace `<date>` with today's date
        2. Make sure to replace `<status>` with the outcome of the deployment (succeeded or failed)
    2. Body: `URL: <url>`
        1. Make sure to replace `<url>` with the link to the knowledge base

The job should fail if any of its steps fail.

## Deliverables

Once you've successfully completed your project, you need to submit the following:

1. The `deploy.yml` workflow file
2. The link to your repository
3. The link to your published knowledge base website on GitHub pages

## Project Structure

```plaintext
spruecss-eleventy-documentation-template/
├─ node_modules/
├─ dist/
├─ src/
│  ├─ _data/
│  ├─ _includes/
│  ├─ css/
│  ├─ filters/
│  ├─ font/
│  ├─ img/
│  ├─ js/
│  ├─ posts/
│  ├─ scss/
│  ├─ shortcodes/
│  ├─ transforms/
│  ├─ changelog.md
│  ├─ faq.md
│  ├─ index.md
├─ .eleventy.js
├─ package.json
├─ README.md
├─ ...
```

- **_data**: Some global data, like the name of your site and helpers like the active navigation element or current year.
- **__includes**: All of the layout and partial templates.
- **css**: The compiled CSS.
- **filters**: The additional filters that you can use.
- **font**: The custom fonts.
- **img**: The static image files.
- **posts**: The markdown contents.
- **scss**: The Sass files.
- **shortcodes**: The available shortcodes.
- **transforms**: The transformations.

### Important tips to run the project

1. To run all the linters in the project, you can use the following command:

    ```bash
    $ npm run lint:all

    ...
    ```

1. To run the URL checker script, you can use the following command:

    ```bash
    $ node url-checker.js -t <timeout> -p <path>

        Options:
        -t, --timeout <timeout>  The timeout in seconds for each request. Default is 5000.
        -d, --directory <directory path> The path to the posts directory. Default is src/posts.
    ```

1. In order to fetch the repository name from a variable with the format: `owner/repository`, one way would be to use `cut`:

    ```bash
    $ echo "owner/repository" | cut -d'/' -f2-

    repository
    ```

1. To build the site so that it's compatible with GitHub Pages, you can use the following commands:

    ```bash
    $ PATH_PREFIX="<name of your repository>"
    $ npm run prod

    ...
    ```


## Project Rubric
Use this project rubric to understand and assess the project criteria.

### Workflow Setup
<table border=2>
    <tr>
        <th>Criteria</th>
        <th>Submission Requirements</th>
    </tr>
    <tr>
        <td>Create GitHub Action workflow files</td>
        <td>
            <ul>
                <li>Demonstrate your ability to write GitHub Action workflows in YAML syntax.
                <li>The workflow will have at least 3 distinct jobs (test, build, deploy).
                <li>If the jobs are named differently, the names selected should be adequate and fit for purpose.
                <li>All the jobs in the workflow need to run without any errors.
                <li>Your repository should show successful workflow runs.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Configure workflows for different triggers with appropriate filters</td>
        <td>
            <ul>
                <li>A workflow run must be triggered (at least) by push, pull_request, and workflow_dispatch events.
                <li>Branch and path filters must be applied as per the requirements provided in the project description.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Configure permissions used by the workflow jobs</td>
        <td>
            <ul>
                <li>Do not overuse and request unnecessary permissions for their workflow runs. Only use what is necessary for the workflow to meet the requirements.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Correctly configure concurrency</td>
        <td>
            <ul>
                <li>Use concurrency to limit the number of simultaneous workflow runs.
                <li>If 2 workflows are triggered by 2 distinct events, only the most recent one should be allowed to run. The previous one (or many) must be canceled.
                <li>The concurrency group name that you choose must be relevant and lead to the expected behavior.
            </ul>
        </td>
    </tr>
</table>

### Test Job
<table border=2>
    <tr>
        <th>Criteria</th>
        <th>Submission Requirements</th>
    </tr>
    <tr>
        <td>Use of the appropriate GitHub-hosted runner type</td>
        <td>
            <ul>
                <li>Select an adequate GitHub-hosted runner for your job, which is ubuntu-latest.
                <li>If you choose a macOS or Windows runner, justify its usage otherwise, you are inefficiently using costly resources.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Configure environment variables at the job level</td>
        <td>
            <ul>
                <li>Configure repository environment variables to be available for the test job. The repository variables will be used in one of the steps and must be made available beforehand.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Use 1st party actions</td>
        <td>
            <ul>
                <li>Use <a href="https://github.com/actions/checkout"><code>actions/checkout@v4</code></a> to check out a copy of their repository for the test job.
                <li>Use <a href="https://github.com/actions/setup-node"><code>actions/setup-node@v4</code></a> to install and configure Node version 18 or later.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Use <a href="https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsrun"><code>jobs.&lt;job_id>.steps[*].run</code></a> to run shell commands
        </td>
        <td>
            <ul>
                <li>Adequately use <a href="https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsrun"><code>jobs.&lt;job_id>.steps[*].run</code></a> to run shell commands
                <li>Install dependencies
                <li>Run the linters and other test scripts
            </ul>
        </td>
    </tr>
    <tr>
        <td>Use the cache service to speed up workflow runs</td>
        <td>
            <ul>
                <li>Use and properly configure <a href="https://github.com/actions/cache"><code>actions/cache@v4</code></a> to store the node_modules folder with a good cache key to prevent the costly download of dependencies in the build job but also in subsequent workflow runs.
            <ul>
        </td>
    </tr>
</table>

### Build Job
<table border=2>
    <tr>
        <th>Criteria</th>
        <th>Submission Requirements</th>
    </tr>
    <tr>
        <td>Use job dependency with <a href="https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idneeds"><code>jobs.&lt;job_id>.needs</code></a></td>
        <td>
            <ul>
                <li>Use <a href="https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idneeds"><code>jobs.&lt;job_id>.needs</code></a> to make sure that the build job does not run if the test job fails.
                <li>This is an important requirement.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Use 1st party actions</td>
        <td>
            <ul>
                <li>Use <a href="https://github.com/actions/checkout"><code>actions/checkout@v4</code></a> to check out a copy of their repository for the test job.
                <li>Use <a href="https://github.com/actions/setup-node"><code>actions/setup-node@v4</code></a> to install and configure Node version 18 or later.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Use the cache service to speed up workflow runs</td>
        <td>
            <ul>
                <li>Use and properly configure <a href="https://github.com/actions/cache"><code>actions/cache@v4</code></a> to store the node_modules folder with a good cache key to prevent the costly download of dependencies in the build job but also in subsequent workflow runs.
            <ul>
        </td>
    </tr>
    <tr>
        <td>Define step level dynamic environment variable</td>
        <td>
        Contrary to repository variables, environment variables can also be defined at the level of a step and can be dynamic.
            <ul>
                <li>Define the <code>PATH_PREFIX</code> variable which should contain their repository name between 2 forward slashes, for example: <code>/&lt;repository name>/</code>
                <li>This variable will be used to configure the <code>pathPrefix</code> value for 11ty to make sure the static assets and urls are properly configured to work with GitHub pages.
            </ul>
        </td>
    </tr>
  <tr>
        <td>Use GitHub Actions artifacts to store the build output</td>
        <td>
            <ul>
                <li>Use GitHub Actions artifacts to upload a zip file containing the build artifacts, mainly the contents of the <code>_site/</code> folder.
                <li>Use <a href="https://github.com/actions/upload-pages-artifact"><code>actions/upload-pages-artifact@v3</code></a> to upload the folder.
            <ul>
        </td>
    </tr>
</table>

### Deploy Job
<table border=2>
    <tr>
        <th>Criteria</th>
        <th>Submission Requirements</th>
    </tr>
    <tr>
        <td>Correctly define the GitHub Pages environment</td>
        <td>
            <ul>
                <li>Correctly define the GitHub pages environment required for the deploy job.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Deploy correctly to GitHub Pages</td>
        <td>
            <ul>
                <li>The output of the build job must be correctly deployed to GitHub pages so that when anyone navigates to the GitHub pages URL, they will see a fully functioning knowledge base website.
                <li>All the links must work and all assets must be loaded correctly. If any assets are missing (CSS, Javascript, or Images) something is misconfigured.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Correctly using <code>GITHUB_TOKEN</code> to authenticate to the GitHub APIs</td>
        <td>
            <ul>
                <li>Use <code>GITHUB_TOKEN</code> with the <code>issues: write</code> permission to authenticate to the GitHub APIs in order to create the summary issue.
            </ul>
        </td>
    </tr>
    <tr>
        <td>Must use GitHub CLI to create the summary issue</td>
        <td>
            <ul>
                <li>Use the authenticated GitHub CLI to create the summary issue.
                <li>The title and body of the issue must be as specified in the requirements for this project.
                <li>The issue must be created successfully.
            </ul>
        </td>
    </tr>
</table>

<table border=1>
    <tr><td>
    <h3>Suggestions to Make Your Project Stand Out</h3>
    <ol>
        <li>Provide more insightful information in the summary issue created after a successful deployment. One idea would be to include links to all the changes that were deployed or even a file diff.
        <li>Combine the repository checkout, Node setup and node_modules caching steps into a reusable. workflow that is hosted in a separate repository
        <li>Create an additional script to automatically test some functionality of the knowledge base, store it in script/ and add it to your test job.
    </ol>
    </td></tr>
</table>
