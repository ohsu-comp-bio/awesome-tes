<h1 align="center">
  Awesome GA4GH TES
  <a href="https://www.ga4gh.org/"/>
  <img title="GA4GH Logo" width="10%" align="middle" src="https://github.com/user-attachments/assets/dae9d663-fa05-470a-a4dc-4ea88c68a2c2"/>
  </a>
</h1>

<div align="center">
  <a href="https://github.com/ohsu-comp-bio/awesome-tes/issues/new?assignees=&labels=bug&template=01_BUG_REPORT.md&title=bug%3A+">Report a Bug</a>
  ·
  <a href="https://github.com/ohsu-comp-bio/awesome-tes/issues/new?assignees=&labels=enhancement&template=02_FEATURE_REQUEST.md&title=feat%3A+">Request a Feature</a>
  ·
  <a href="https://github.com/ohsu-comp-bio/awesome-tes/discussions">Ask a Question</a>
</div>

<div align="center">
<br />

[![Project license](https://img.shields.io/github/license/ohsu-comp-bio/awesome-ga4gh.svg)](LICENSE)
[![Pull Requests welcome](https://img.shields.io/badge/PRs-welcome-ff69b4.svg)](https://github.com/ohsu-comp-bio/awesome-ga4gh/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)
[![Coded with love by ga4gh](https://img.shields.io/badge/Coded%20with%20%E2%99%A5%20by-GA4GH-blue)](https://github.com/ga4gh)

<br />
</div>

- [Overview](#overview)
  - [Paper](#paper)
- [TES Ecosystem](#tes-ecosystem)
- [Design](#design)
  - [Use Cases](#use-cases)
  - [Architecture](#architecture)
  - [Example TES Packet](#example-tes-packet)
- [Additional Resources](#additional-resources)
- [Contributing](#contributing)

# Overview

The Global Alliance for Genomics and Health (GA4GH) is an international coalition formed to enable the responsible, voluntary, and secure sharing of genomic and health-related data. This awesome list collects resources, projects, tools, and standards from the GA4GH ecosystem that support the mission of enabling responsible data sharing.

## Paper

The following resources are adapted from [The GA4GH Task Execution API: Enabling Easy Multi Cloud Task Execution](https://arxiv.org/pdf/2405.00013)

```latex
@article{kanitz2024ga4gh,
  title={The GA4GH Task Execution API: Enabling Easy Multi Cloud Task Execution},
  author={Kanitz, Alexander and McLoughlin, Matthew H and Beckman, Liam and Malladi, Venkat S and Ellrott, Kyle},
  journal={Computing in Science \& Engineering},
  year={2024},
  publisher={IEEE}
}
```

# TES Ecosystem

A listing of available servers, proxy and client implementations that utilize the TES API.

| Type   | Project                 | Description                                                                                                                  | Source                                                            |
|--------|-------------------------|------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| API    | TES                     | OpenAPI definition of the specification                                                                                      | [GitHub](https://github.com/ga4gh/task-execution-schemas)         |
| API    | TES                     | Conformance test runner                                                                                                      | [GitHub](https://github.com/elixir-cloud-aai/openapi-test-runner) |
| Server | Funnel                  | TES server implementation for HPC/HTS systems including AWS Batch, Google Cloud, Kubernetes, Slurm, GridEngine, and HTCondor | [GitHub](https://github.com/ohsu-comp-bio/funnel)                 |
| Server | Pulsar                  | TES server implementation for the Galaxy/Pulsar federated distributed network                                                | [Docs](https://pulsar.readthedocs.io)                             |
| Server | TES-Azure               | TES server implementation for Microsoft Azure                                                                                | [GitHub](https://github.com/microsoft/ga4gh-tes)                  |
| Server | TESK                    | TES server implementation for Kubernetes/Native Cloud systems                                                                | [GitHub](https://github.com/elixir-cloud-aai/TESK)                |
| Proxy  | proTES                  | Proxy service for injecting middleware into GA4GH TES requests                                                               | [GitHub](https://github.com/elixir-cloud-aai/proTES)              |
| Client | Cromwell                | Workflow management system for executing composed workflows in the Workflow Definition Language (WDL) DSL                    | [Docs](https://cromwell.readthedocs.io)                           |
| Client | cwl-tes                 | Workflow management system for executing workflows in the Common Workflow Language (CWL) DSL                                 | [GitHub](https://github.com/ohsu-comp-bio/cwl-tes)                |
| Client | ELIXIR Cloud Components | Web Component library for interacting with TES services (and other GA4GH APIs)                                               | [Site](https://elixir-cloud-components.vercel.app)                |
| Client | Nextflow                | Workflow management system for executing workflows composed in the Nextflow DSL                                              | [Site](https://nextflow.io)                                       |
| Client | py-tes                  | Python client library for interacting with TES services                                                                      | [GitHub](https://github.com/ohsu-comp-bio/py-tes)                 |
| Client | Snakemake               | Workflow management system for executing workflows composed in the Snakemake DSL                                             | [Site](https://snakemake.github.io)                               |
| Client | Toil                    | Workflow management system for executing workflows composed in the Toil and CWL DSLs                                         | [Docs](https://toil.readthedocs.io)                               |

# Design

## Use Cases

<div align=center>
  <img title="Common TES use cases" width="80%" src="https://github.com/user-attachments/assets/3219b125-cfcf-4204-995d-5452819168ee"/>
</div>

Common TES use cases. The TES API wraps around compute environments providing a standard way of executing tasks.  Researchers can write and package their tasks and data in a domain-specific language (DSL) workflow language. They then hand the orchestration of the tasks over to the respective workflow management systems. The workflow management systems can then make use of TES clients to distribute tasks across different environments.

Alternatively, users can submit individual tasks to TES servers directly via command-line (CLI) or graphical user (GUI) interfaces. Thus, TES makes it easier for researchers to make use of a variety of compute environments seamlessly. Applications can support new compute environments by integrating with TES API, rather than develop unique connections for each environment. 

## Architecture

<div align=center>
  <img title="TES Execution Architecture" width="80%" src="https://github.com/user-attachments/assets/3c99bba4-c1a8-4468-9c73-8b3c47e82891"/>
</div>

TES Execution Architecture. An outline of the separate layers found in current TES service implementations. The client talks to a server, which is responsible for allocating a worker node on an HPC/HTC or cloud infrastructure. The TES worker is responsible for transferring inputs, running user code, capturing logging and storing outputs. 

## Example TES Packet

An example TES task demonstrating the use of inputs, outputs, and logging.

### Input/Output Defintions

```json
  "inputs": [
    {
      "name": "input-genome-data",
      "url": "gs://genomics-bucket/input-data/genome-data.bam",
      "path": "/data/genome-data.bam",
      "type": "FILE"
    },
    {
      "name": "reference-genome",
      "url": "gs://genomics-bucket/reference/human-reference.fa",
      "path": "/data/human-reference.fa",
      "type": "FILE"
    }
  ],
  "outputs": [
    {
      "name": "output-processed-data",
      "url": "gs://genomics-bucket/processed-data/processed-output.bam",
      "path": "/output/processed-output.bam",
      "type": "FILE"
    },
    {
      "name": "log-file",
      "url": "gs://genomics-bucket/logs/task-log.txt",
      "path": "/output/task-log.txt",
      "type": "FILE"
    }
  ],
```


### Resource Defintions

```json
  "resources": {
    "cpu_cores": 8,
    "ram_gb": 32,
    "disk_gb": 100,
    "preemptible": true,
    "zones": ["us-west1-a", "us-west1-b"],
    "backend_parameters": {
      "VmSize": "Standard_D64_v3"
    }
  },
  "volumes": ["/mnt/workdir"],
```

### Executor Command Lines

```json
  "executors": [
    {
      "image": "bioinformatics/pipeline",
      "command": [
        "bash",
        "-c",
        "/tools/process-genome.sh",
        "/data/genome-data.bam",
        "/data/human-reference.fa",
        "/output/processed-output.bam"
      ],
      "stdout": "/output/task-log.txt",
      "stderr": "/output/task-error-log.txt",
      "workdir": "/mnt/workdir",
      "env": {
        "GENOME_ENV": "production",
        "MAX_THREADS": "8"
      }
    }
  ],
```

### User Provided Tags

```json
  "tags": {
    "department": "bioinformatics",
    "project": "genome-analysis"
  },
```


### Logs Added by Server

```json
"logs": [
    {
      "start_time": "2023-12-25T00:00:00+00:00",
      "end_time": "2023-12-25T12:12:12+00:00",
      "logs": [
        {
          "start_time": "2023-12-25T00:00:01+00:00",
          "end_time": "2023-12-25T12:12:12+00:00",
          "exit_code": 0
        }
      ]
    }
  ]
```

<details><summary>Full Packet Example</summary>

```json
{
  "inputs": [
    {
      "name": "input-genome-data",
      "url": "gs://genomics-bucket/input-data/genome-data.bam",
      "path": "/data/genome-data.bam",
      "type": "FILE"
    },
    {
      "name": "reference-genome",
      "url": "gs://genomics-bucket/reference/human-reference.fa",
      "path": "/data/human-reference.fa",
      "type": "FILE"
    }
  ],
  "outputs": [
    {
      "name": "output-processed-data",
      "url": "gs://genomics-bucket/processed-data/processed-output.bam",
      "path": "/output/processed-output.bam",
      "type": "FILE"
    },
    {
      "name": "log-file",
      "url": "gs://genomics-bucket/logs/task-log.txt",
      "path": "/output/task-log.txt",
      "type": "FILE"
    }
  ],
  "resources": {
    "cpu_cores": 8,
    "ram_gb": 32,
    "disk_gb": 100,
    "preemptible": true,
    "zones": ["us-west1-a", "us-west1-b"],
    "backend_parameters": {
      "VmSize": "Standard_D64_v3"
    }
  },
  "volumes": ["/mnt/workdir"],
  "executors": [
    {
      "image": "bioinformatics/pipeline",
      "command": [
        "bash",
        "-c",
        "/tools/process-genome.sh",
        "/data/genome-data.bam",
        "/data/human-reference.fa",
        "/output/processed-output.bam"
      ],
      "stdout": "/output/task-log.txt",
      "stderr": "/output/task-error-log.txt",
      "workdir": "/mnt/workdir",
      "env": {
        "GENOME_ENV": "production",
        "MAX_THREADS": "8"
      }
    }
  ],
  "tags": {
    "department": "bioinformatics",
    "project": "genome-analysis"
  },
  "logs": [
    {
      "start_time": "2023-12-25T00:00:00+00:00",
      "end_time": "2023-12-25T12:12:12+00:00",
      "logs": [
        {
          "start_time": "2023-12-25T00:00:01+00:00",
          "end_time": "2023-12-25T12:12:12+00:00",
          "exit_code": 0
        }
      ]
    }
  ]
}
```

</details> 

# Additional Resources

- [GA4GH](https://www.ga4gh.org): Global Alliance for Genomics and Health
- [DRS](https://www.ga4gh.org/product/data-repository-service-drs/): Data Repository Service
- [TRS](https://www.ga4gh.org/product/tool-registry-service-trs/): Tool Registry Service
- [TES](https://www.ga4gh.org/product/task-execution-service-tes/): Task Execution Service
- [WES](https://www.ga4gh.org/product/workflow-execution-service-wes/): Workflow Execution Service
- [Implementations](https://www.ga4gh.org/our-products/implementations/#): Implementations of GA4GH Producs

# Contributing

If you're working with TES or would like to add any additional programs here, please reach out or create a new PR or issue. We'd love to hear about it!
