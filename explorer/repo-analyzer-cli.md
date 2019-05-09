# Using the Analyzer command line interface

The Analyzer CLI is part of Stoplight Explorer. It generates the data that Explorer uses in its searches and dashboards. Analyzer stores changes and information in a Postgres database. You can search and filter this information in the Explorer module in Stoplight.

## Run Analyzer

1. Install the Stoplight CLI:
```
npm i -g @stoplight/cli
```
2. Find the token for the project you want to analyze:
    1. Log in to Stoplight as a user with site admin permissions.
    2. Navigate to the **Site Admin** panel (**admin** -> **Site Admin**)
    3. Select **Projects**.
    4. Locate the project you want to analyze.
    5. Select **Analyze**. This opens a modal with instructions on how to analyze the project, and the project token.
3. Navigate into the root directory of the local git repository associated with this project.
4. Run the following command:
    ```
    stoplight analyze -t $project_token -a $api_host
    ```
    Where `$project_token` is the token you retrieved in step two, and $api_host is the URL of the Stoplight API.

<!-- TODO: confirm how they obtain the URL of the Stoplight API -->

### Optional flags and variables:

- `-d` allows you to do a dry run. It prevents Stoplight from persisting any changes.
- `--verbose` changes the log level to debug, providing more detailed logging while Analyzer runs.
- You can use the `SL_API_HOST` environment variable in place of the `-a` flag and `$api_host` value.

## Run Analyzer without persisting changes

You can run Analyzer without updating the data that Explorer uses. This is a dry run. This is useful if you want to check for changes to your project without affecting the data that Explorer uses.

### Dry run with baseline

A dry run with baseline looks up the existing data in Explorer and displays changes against it.

```
stoplight analyze -t $project_token -a $api_host -d
```

You must use both the `-t` and `-a` flags to do a dry run with baseline.

### Dry run without baseline

A dry run without baseline does not check for existing data or a previous project analysis. Analyzer assumes this is the first time analyzing this project. The results will appear as if all project contents are new.

```
stoplight analyze -d
```