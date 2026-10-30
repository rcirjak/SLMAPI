# Slow Language Model API (SLM)

**Resource-aware, asynchronous LLM inference for consumer hardware.**

Created by **Roko Čirjak** · Licensed under the **MIT License**

SLM is an experimental inference orchestration layer built around llama.cpp, designed to make oversized language models more accessible on memory-constrained consumer hardware.

Instead of treating inference as an immediate API request, SLM treats it as a background job: applications submit inference tasks, receive a job ID, and periodically check execution status or retrieve completed results.

## Core Features

- **Asynchronous inference** — Submit jobs without waiting for generation to finish.
- **Resource-aware execution** — Manage CPU, RAM, GPU and disk I/O budgets.
- **Background scheduling** — Run inference with minimal interference to foreground applications.
- **Memory-constrained loading** — Leverage GGUF, memory mapping, lazy loading and Linux memory management.
- **Model management** — Load, reuse and unload models according to available resources.
- **Persistent jobs** — Maintain job state and results across service restarts.
- **System monitoring** — Track resource consumption, memory pressure and inference performance.
- **Sequential execution** — Queue jobs and control concurrency to prevent resource exhaustion.

## Architecture

Applications → SLM REST API → Job Scheduler → Resource Manager → Inference Worker → llama.cpp → GGUF Model

SQLite provides persistent job and result storage.

## Example

Submit an inference job:

    POST /v1/jobs

    {
      "model": "qwen-32b",
      "prompt": "Explain how transformer models work.",
      "resources": {
        "priority": "background",
        "max_ram": "10GB",
        "max_gpu_memory": "4GB",
        "max_cpu_threads": 4
      },
      "max_tokens": 2048
    }

Receive a job ID:

    {
      "job_id": "job_123",
      "status": "queued"
    }

Check execution status:

    GET /v1/jobs/job_123

Retrieve the result:

    GET /v1/jobs/job_123/result

## Technology Stack

- **Inference:** llama.cpp
- **Model format:** GGUF
- **API:** Python, FastAPI
- **Job persistence:** SQLite
- **Scheduling:** Python asyncio
- **Resource isolation:** Linux cgroups v2
- **Process management:** systemd
- **Resource monitoring:** Linux PSI, psutil, NVIDIA SMI

## Project Objective

SLM explores an alternative approach to local LLM inference: trading execution time for reduced resource requirements.

Through asynchronous scheduling, memory-aware model loading, background execution and dynamic resource allocation, SLM aims to make increasingly large open-weight models accessible on ordinary consumer hardware.

The initial prototype focuses on resource management, memory-constrained inference and persistent job orchestration.

Advanced tensor streaming, Mixture-of-Experts expert paging and resumable inference are planned research directions.

## Philosophy

**If a model can run on your hardware eventually, it shouldn't matter that it takes longer.**

## License

This project is licensed under the [MIT License](https://opensource.org/license/mit/).

---

Created by **Roko Čirjak**.
