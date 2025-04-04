---
title: Analyzing your code with CodeQL queries
intro: 'You can run queries against a {% data variables.product.prodname_codeql %} database extracted from a codebase.'
product: '{% data reusables.gated-features.codeql %}'
shortTitle: Analyzing code
versions:
  fpt: '*'
  ghes: '*'
  ghec: '*'
topics:
  - Code Security
  - Code scanning
  - CodeQL
redirect_from:
  - /code-security/codeql-cli/analyzing-databases-with-the-codeql-cli
  - /code-security/codeql-cli/using-the-codeql-cli/analyzing-databases-with-the-codeql-cli
---

## About analyzing databases with the {% data variables.product.prodname_codeql_cli %}

{% data reusables.code-scanning.codeql-cli-version-ghes %}

To analyze a codebase, you run queries against a {% data variables.product.prodname_codeql %} database extracted from the code. {% data variables.product.prodname_codeql %} analyses produce results that can be uploaded to {% data variables.product.github %} to generate code scanning alerts.

## Prerequisites

Before starting an analysis you must:

* [Set up the {% data variables.product.prodname_codeql_cli %}](/code-security/codeql-cli/getting-started-with-the-codeql-cli/setting-up-the-codeql-cli) to run commands locally.
* [Create a {% data variables.product.prodname_codeql %} database](/code-security/codeql-cli/getting-started-with-the-codeql-cli/preparing-your-code-for-codeql-analysis) for the source code you want to analyze.

The simplest way to run `codeql database analyze` is using the standard queries included in the {% data variables.product.prodname_codeql_cli %} bundle.

## Running `codeql database analyze`

When you run `database analyze`, it:

1. Optionally downloads any referenced {% data variables.product.prodname_codeql %} packages that are not available locally.
1. Executes one or more query files, by running them over a {% data variables.product.prodname_codeql %} database.
1. Interprets the results, based on certain query metadata, so that alerts can be
displayed in the correct location in the source code.
1. Reports the results of any diagnostic and summary queries to standard output.

You can analyze a database by running the following command:

```shell
codeql database analyze <database> --format=<format> --output=<output> <query-specifiers>...
```

> [!NOTE]
> If you analyze more than one {% data variables.product.prodname_codeql %} database for a single commit, you must specify a SARIF category for each set of results generated by this command. When you upload the results to {% data variables.product.github %}, {% data variables.product.prodname_code_scanning %} uses this category to store the results for each language separately. If you forget to do this, each upload overwrites the previous results.
>
> ```shell
> codeql database analyze <database> --format=<format> \
>     --sarif-category=<language-specifier> --output=<output> \
>     <packs,queries>
> ```

You must specify `<database>`, `--format`, and `--output`. You can specify additional options depending on what analysis you want to do.

| Option | Required | Usage |
|--------|:--------:|-----|
| `<database>` | {% octicon "check" aria-label="Required" %} | Specify the path for the directory that contains the {% data variables.product.prodname_codeql %} database to analyze. |
| `<packs,queries>` | {% octicon "x" aria-label="Optional" %} | Specify {% data variables.product.prodname_codeql %} packs or queries to run. To run the standard queries used for {% data variables.product.prodname_code_scanning %}, omit this parameter. To see the other query suites included in the {% data variables.product.prodname_codeql_cli %} bundle, run `codeql resolve queries`. The suites listed there can be provided with or without the `.qls` extension. For information about creating your own query suite, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/creating-codeql-query-suites) in the documentation for the {% data variables.product.prodname_codeql_cli %}. |
| <code><span style="white-space: nowrap;">--format</span></code> | {% octicon "check" aria-label="Required" %} | Specify the format for the results file generated during analysis. A number of different formats are supported, including CSV, [SARIF](https://codeql.github.com/docs/codeql-overview/codeql-glossary/#sarif-file), and graph formats. For upload to {% data variables.product.company_short %} this should be: {% ifversion fpt or ghec %}`sarif-latest`{% else %}`sarifv2.1.0`{% endif %}. For more information, see [AUTOTITLE](/code-security/code-scanning/integrating-with-code-scanning/sarif-support-for-code-scanning). |
| <code><span style="white-space: nowrap;">--output</span></code> | {% octicon "check" aria-label="Required" %} | Specify the location where you want to save the SARIF results file, including the desired filename with the `.sarif` extension. |
| <code><span style="white-space: nowrap;">--sarif-category</span></code> | {% octicon "question" aria-label="Required with multiple results sets" %} | Optional for single database analysis. Required to define the language when you analyze multiple databases for a single commit in a repository.<br><br>Specify a category to include in the SARIF results file for this analysis. A category is used to distinguish multiple analyses for the same tool and commit, but performed on different languages or different parts of the code. |
| <code><span style="white-space: nowrap;">--sarif-add-baseline-file-info</span></code> | {% octicon "x" aria-label="Optional" %} | **Recommended.** Use to submit file coverage information to the {% data variables.code-scanning.tool_status_page %}. For more information, see [AUTOTITLE](/code-security/code-scanning/managing-your-code-scanning-configuration/about-the-tool-status-page#how-codeql-defines-scanned-files). |
| <code><span style="white-space: nowrap;">--sarif-include-query-help</span></code> | {% octicon "x" aria-label="Optional" %} | Specify whether to include query help in the SARIF output. One of: `always`: Include query help for all queries. `custom_queries_only` (default): Include query help only for custom queries, that is, queries in query packs which are not of the form `codeql/<lang>-queries`. `never`: Do not include query help for any queries. Any query help for custom queries included in the SARIF output will be displayed in any code scanning alerts for the query. For more information, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/using-custom-queries-with-the-codeql-cli#including-query-help-for-custom-codeql-queries-in-sarif-files). |
| `<packs>` | {% octicon "x" aria-label="Optional" %} | Use if you want to include {% data variables.product.prodname_codeql %} query packs in your analysis. For more information, see [Downloading and using {% data variables.product.prodname_codeql %} packs](/code-security/codeql-cli/getting-started-with-the-codeql-cli/customizing-analysis-with-codeql-packs#downloading-and-using-codeql-query-packs). |
| <code><span style="white-space: nowrap;">--download</span></code> | {% octicon "x" aria-label="Optional" %}  | Use if some of your {% data variables.product.prodname_codeql %} query packs are not yet on disk and need to be downloaded before running queries. |
| <code><span style="white-space: nowrap;">--threads</span></code> | {% octicon "x" aria-label="Optional" %}  | Use if you want to use more than one thread to run queries. The default value is `1`. You can specify more threads to speed up query execution. To set the number of threads to the number of logical processors, specify `0`. |
| <code><span style="white-space: nowrap;">--verbose</span></code> | {% octicon "x" aria-label="Optional" %}  | Use to get more detailed information about the analysis process and diagnostic data from the database creation process. |
| <code><span style="white-space: nowrap;">--threat-model</span></code> | {% octicon "x" aria-label="Optional" %}  | ({% data variables.release-phases.public_preview_caps %}) Use to add threat models to configure additional sources in your {% data variables.product.prodname_codeql %} analysis. During the {% data variables.release-phases.public_preview %}, threat models are supported only by Java analysis. For more information, see [AUTOTITLE](/code-security/codeql-cli/codeql-cli-manual/database-analyze#--threat-modelname). |

> [!NOTE]
> **Upgrading databases**
>
> For databases that were created by {% data variables.product.prodname_codeql_cli %} v2.3.3 or earlier, you will need to explicitly upgrade the database before you can run an analysis with a newer
version of the {% data variables.product.prodname_codeql_cli %}. If this step is necessary, then you will see a message telling you
that your database needs to be upgraded when you run `database analyze`.
>
> For databases that were created by {% data variables.product.prodname_codeql_cli %} v2.3.4 or later, the CLI will implicitly run any required upgrades. Explicitly running the upgrade command is not necessary.

For full details of all the options you can use when analyzing databases, see [AUTOTITLE](/code-security/codeql-cli/codeql-cli-manual/database-analyze).

### Basic example of analyzing a {% data variables.product.prodname_codeql %} database

This example analyzes a {% data variables.product.prodname_codeql %} database stored at `/codeql-dbs/example-repo` and saves the results as a SARIF file: `/temp/example-repo-js.sarif`. It uses `--sarif-category` to include extra information in the SARIF file that identifies the results as JavaScript. This is essential when you have more than one {% data variables.product.prodname_codeql %} database to analyze for a single commit in a repository.

```shell
$ codeql database analyze /codeql-dbs/example-repo \
    javascript-code-scanning.qls --sarif-category=javascript-typescript \
    --format={% ifversion fpt or ghec %}sarif-latest{% else %}sarifv2.1.0{% endif %} --output=/temp/example-repo-js.sarif

> Running queries.
> Compiling query plan for /codeql-home/codeql/qlpacks/codeql-javascript/AngularJS/DisablingSce.ql.
...
> Shutting down query evaluator.
> Interpreting results.
```

### Adding file coverage information to your results for monitoring

You can optionally submit file coverage information to {% data variables.product.github %} for display on the {% data variables.code-scanning.tool_status_page %} for {% data variables.product.prodname_code_scanning %}. For more information about file coverage information, see [AUTOTITLE](/code-security/code-scanning/managing-your-code-scanning-configuration/about-the-tool-status-page#how-codeql-defines-scanned-files).

To include file coverage information with your {% data variables.product.prodname_code_scanning %} results, add the `--sarif-add-baseline-file-info` flag to the `codeql database analyze` invocation in your CI system, for example:

```shell
$ codeql database analyze /codeql-dbs/example-repo \
    javascript-code-scanning.qls --sarif-category=javascript-typescript \
    --sarif-add-baseline-file-info \ --format={% ifversion fpt or ghec %}sarif-latest{% else %}sarifv2.1.0{% endif %} \
    --output=/temp/example-repo-js.sarif
```

## Examples of running database analyses

The following examples show how to run `database analyze` using {% data variables.product.prodname_codeql %} packs, and how to use a local checkout of the {% data variables.product.prodname_codeql %} repository. These examples assume your {% data variables.product.prodname_codeql %} databases have been created in a directory that is a sibling of your local copies of the {% data variables.product.prodname_codeql %} repository.

### Running a {% data variables.product.prodname_codeql %} query pack

To run an existing {% data variables.product.prodname_codeql %} query pack from the {% data variables.product.prodname_dotcom %} {% data variables.product.prodname_container_registry %}, you can specify one or more pack names:

```shell
codeql database analyze <database> microsoft/coding-standards@1.0.0 github/security-queries --format=sarifv2.1.0 --output=query-results.sarif --download
```

This command runs the default query suite of two {% data variables.product.prodname_codeql %} query packs: `microsoft/coding-standards` version 1.0.0 and the latest version of `github/security-queries` on the specified database. For further information about default suites, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/publishing-and-using-codeql-packs).

The `--download` flag is optional. Using it will ensure the query pack is downloaded if it isn’t yet available locally.

### Running a single query

To run a single query over a {% data variables.product.prodname_codeql %} database for a JavaScript codebase,
you could use the following command from the directory containing your database:

```shell
codeql database analyze --download <javascript-database> codeql/javascript-queries:Declarations/UnusedVariable.ql --format=csv --output=js-analysis/js-results.csv
```

This command runs a simple query that finds potential bugs related to unused
variables, imports, functions, or classes—it is one of the JavaScript
queries included in the {% data variables.product.prodname_codeql %} repository. You could run more than one query by
specifying a space-separated list of similar paths.

The analysis generates a CSV file (`js-results.csv`) in a new directory (`js-analysis`).

Alternatively, if you have the {% data variables.product.prodname_codeql %} repository checked out, you can execute the same queries by specifying the path to the query directly:

```shell
codeql database analyze <javascript-database> ../ql/javascript/ql/src/Declarations/UnusedVariable.ql --format=csv --output=js-analysis/js-results.csv
```

You can also run your own custom queries with the `database analyze` command.
For more information about preparing your queries to use with the {% data variables.product.prodname_codeql_cli %},
see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/using-custom-queries-with-the-codeql-cli).

### Running all queries in a directory

You can run all the queries located in a directory by providing the directory
path, rather than listing all the individual query files. Paths are searched
recursively, so any queries contained in subfolders will also be executed.

> [!IMPORTANT]
> You should avoid specifying the root of a core {% data variables.product.prodname_codeql %} query pack when executing `database analyze` as it might contain some special queries that aren’t designed to be used with the command. Rather, run the query pack to include the pack’s default queries in the analysis, or run one of the code scanning query suites.

For example, to execute all Python queries contained in the `Functions` directory in the
`codeql/python-queries` query pack you would run:

```shell
codeql database analyze <python-database> codeql/python-queries:Functions --format=sarif-latest --output=python-analysis/python-results.sarif --download
```

Alternatively, if you have the {% data variables.product.prodname_codeql %} repository checked out, you can execute the
same queries by specifying the path to the directory directly:

```shell
codeql database analyze <python-database> ../ql/python/ql/src/Functions/ --format=sarif-latest --output=python-analysis/python-results.sarif
```

When the analysis has finished, a SARIF results file is generated. Specifying `--format=sarif-latest` ensures
that the results are formatted according to the most recent SARIF specification
supported by {% data variables.product.prodname_codeql %}.

### Running a subset of queries in a {% data variables.product.prodname_codeql %} pack

If you are using {% data variables.product.prodname_codeql_cli %} v2.8.1 or later, you can include a path at the end of a pack specification to run a subset of queries inside the pack. This applies to any command that locates or runs queries within a pack.

The complete way to specify a set of queries is in the form `scope/name@range:path`, where:

* `scope/name` is the qualified name of a {% data variables.product.prodname_codeql %} pack.

* `range` is a [semver range](https://docs.npmjs.com/cli/v6/using-npm/semver#ranges).

* `path` is a file system path to a single query, a directory containing queries, or a query suite file.

When you specify a `scope/name`, the `range` and `path` are
optional. If you omit a `range` then the latest version of the
specified pack is used. If you omit a `path` then the default query suite
of the specified pack is used.

The `path` can be one of a `\*.ql` query file, a directory
containing one or more queries, or a `.qls` query suite file. If
you omit a pack name, then you must provide a `path`,
which will be interpreted relative to the working directory
of the current process.

If you specify a `scope/name` and `path`, then the `path` cannot
be absolute. It is considered relative to the root of the {% data variables.product.prodname_codeql %}
pack.

To analyze a database using all queries in the `experimental/Security` folder within the `codeql/cpp-queries` {% data variables.product.prodname_codeql %} pack you can use:

```shell
codeql database analyze --format=sarif-latest --output=results <db> \
    codeql/cpp-queries:experimental/Security
```

To run the `RedundantNullCheckParam.ql` query in the `codeql/cpp-queries` {% data variables.product.prodname_codeql %} pack use:

```shell
codeql database analyze --format=sarif-latest --output=results <db> \
    'codeql/cpp-queries:experimental/Likely Bugs/RedundantNullCheckParam.ql'
```

To analyze your database using the `cpp-security-and-quality.qls` query suite from a version of the `codeql/cpp-queries` {% data variables.product.prodname_codeql %} pack that is >= 0.0.3 and < 0.1.0 (the highest compatible version will be chosen) you can use:

```shell
codeql database analyze --format=sarif-latest --output=results <db> \
   'codeql/cpp-queries@~0.0.3:codeql-suites/cpp-security-and-quality.qls'
```

If you need to reference a query file, directory, or suite whose path contains a literal `@` or `:`, you can prefix the query specification with `path:` like so:

```shell
codeql database analyze --format=sarif-latest --output=results <db> \
    path:C:/Users/ci/workspace@2/security/query.ql
```

For more information about {% data variables.product.prodname_codeql %} packs, see [AUTOTITLE](/code-security/codeql-cli/getting-started-with-the-codeql-cli/customizing-analysis-with-codeql-packs).

### Running query suites

To run a query suite on a {% data variables.product.prodname_codeql %} database for a C/C++ codebase,
you could use the following command from the directory containing your database:

```shell
codeql database analyze <cpp-database> codeql/cpp-queries:codeql-suites/cpp-code-scanning.qls --format=sarifv2.1.0 --output=cpp-results.sarif --download
```

This command downloads the `codeql/cpp-queries` {% data variables.product.prodname_codeql %} query pack, runs the analysis, and generates a file in the SARIF version 2.1.0 format that is supported by all versions of {% data variables.product.prodname_dotcom %}. This file can be uploaded to {% data variables.product.prodname_dotcom %} by executing `codeql github upload-results` or the code scanning API.
For more information, see [AUTOTITLE](/code-security/codeql-cli/getting-started-with-the-codeql-cli/uploading-codeql-analysis-results-to-github)
or [AUTOTITLE](/rest/code-scanning).

{% data variables.product.prodname_codeql %} query suites are `.qls` files that use directives to select queries to run
based on certain metadata properties. The standard {% data variables.product.prodname_codeql %} packs have metadata that specify
the location of the query suites used by code scanning, so the {% data variables.product.prodname_codeql_cli %} knows where to find these
suite files automatically, and you don’t have to specify the full path on the command line.
For more information, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/creating-codeql-query-suites).

For information about creating custom query suites, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/creating-codeql-query-suites).

### Including model packs to add potential sources of tainted data

{% data reusables.code-scanning.beta-threat-models-cli %}

You can configure threat models in a {% data variables.product.prodname_code_scanning %} analysis. For more information, see [Threat models for Java and Kotlin](https://codeql.github.com/docs/codeql-language-guides/customizing-library-models-for-java-and-kotlin/#threat-models) and [Threat models for C#](https://codeql.github.com/docs/codeql-language-guides/customizing-library-models-for-csharp/#threat-models) in the {% data variables.product.prodname_codeql %} documentation.

```shell
$ codeql database analyze /codeql-dbs/my-company --format=sarif-latest \
  --threat-model=local \
  --output=/temp/my-company.sarif codeql/java-queries
```

In this example, the relevant queries in the standard query pack `codeql/java-queries` will use the `local` threat model as well as the default threat model for `remote` dataflow sources. You should use the `local` threat model if you consider data from local sources (for example: file systems, command-line arguments, databases, and environment variables) to be potential sources of tainted data for your codebase.

## Results

You can save analysis results in a number of different formats, including SARIF and CSV.

The SARIF format is designed to represent the output of a broad range of static analysis tools. For more information, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/sarif-output).

For more information about what the results look like in CSV format, see [AUTOTITLE](/code-security/codeql-cli/using-the-advanced-functionality-of-the-codeql-cli/csv-output).

Results files can be integrated into your own code-review or debugging infrastructure. For example, SARIF file output can be used to highlight alerts in the correct location in your source code using a SARIF viewer plugin for your IDE.

## Viewing log and diagnostic information

When you analyze a {% data variables.product.prodname_codeql %} database using a {% data variables.product.prodname_code_scanning %} query suite, in addition to generating detailed information about alerts, the CLI reports diagnostic data from the database generation step and summary metrics. If you choose to generate SARIF output, the additional data is also included in the SARIF file. For repositories with few alerts, you may find this information useful for determining if there are genuinely few problems in the code, or if there were errors generating the {% data variables.product.prodname_codeql %} database. For more detailed output from `codeql database analyze`, use the `--verbose` option.

For more information about the type of diagnostic information available, see [AUTOTITLE](/code-security/code-scanning/managing-your-code-scanning-configuration/viewing-code-scanning-logs#about-analysis-and-diagnostic-information).

You can choose to export and upload diagnostic information to {% data variables.product.github %} even if a {% data variables.product.prodname_codeql %} analysis fails. For more information, see [AUTOTITLE](/code-security/codeql-cli/getting-started-with-the-codeql-cli/uploading-codeql-analysis-results-to-github#uploading-diagnostic-information-to-github-if-the-analysis-fails).

## Next steps

* To learn how to upload your {% data variables.product.prodname_codeql %} analysis results to {% data variables.product.github %}, see [AUTOTITLE](/code-security/codeql-cli/getting-started-with-the-codeql-cli/uploading-codeql-analysis-results-to-github).
