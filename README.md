# Celery Core

*An educational project showcasing how to use Celery with synchronous, multiprocessing, and cooperative multitasking approaches.*

> [!WARNING]  
> **Project Archived**
> 
> This project is no longer actively maintained. 
> 
> **Context:**  
> Originally, this repository began as a modular component of a large-scale university assignment. Rather than simply completing the task and discarding the code, I chose to expand it into a structured mini-project to deeply explore how to work with Celery in practice. Over time, however, I decided to consolidate all the modular knowledge I gained in web technologies into a single, unified educational project. Maintaining "perfect educational code" for Celery across multiple separate repositories became inefficient and fragmented.
> 
> **Reason for Archiving:**  
> Consequently, I made the decision to archive this standalone project to consolidate and maintain the highest quality, up-to-date code in a single location. 
> 
> This repository remains available purely as an educational reference for those interested in a foundational understanding of how to work with Celery in practice. For the most up-to-date and comprehensive implementation, please refer to my main unified project:
> 
> - [Pose Estimation Service](https://github.com/snegoviol/pose-estimation-service)
> 
> Additionally, you can explore further examples of my architecture and development approach in this repository:
> - [Fullstack Core](https://github.com/snegoviol/fullstack-core)

## Project Structure

```
celery-core/
├── app/                        # Main application code
├── docker-compose.yml          # Docker Compose configuration for Redis broker
├── .env.example                # Example environment variables file
├── justfile                    # Project-specific commands using Just
├── pixi.lock                   # Locked dependency versions for reproducible environments
├── pixi.toml                   # Pixi project configuration: environments, dependencies, and platforms
└── playground-testing.ipynb    # Jupyter notebook for playground Celery testing
```

The `app/` directory includes its own `README.md` with detailed information about its contents and usage, and every file (except `docker-compose.yml`) contains comprehensive inline comments to explain the code.

## Redis as Message Broker

Redis was chosen as the message broker due to its simplicity, reliability, and familiarity from the previous [redis-core](https://github.com/snegoviol/redis-core) project. Since the primary focus here is on demonstrating Celery's capabilities, the `docker-compose.yml` file contains a minimal configuration for the Redis broker, omitting the more detailed comments found in the `redis-core` project's compose files.

## Dependencies Overview

| Dependency | Repository | What it is |
| :--- | :--- | :--- |
| **CPython** | [python/cpython](https://github.com/python/cpython) | The de facto standard implementation of the Python programming language. |
| **celery** | [celery/celery](https://github.com/celery/celery) | Distributed task queue framework for Python supporting multiple execution models: synchronous, multiprocessing, and cooperative multitasking. |
| **pydantic-settings** | [pydantic/pydantic-settings](https://github.com/pydantic/pydantic-settings) | Pydantic-powered library for managing application configuration and environment variables with strong typing, validation, and seamless `.env` support. |
| **redis-py** | [redis/redis-py](https://github.com/redis/redis-py) | The official Python client for Redis, used here as a message broker for Celery task queues. |
| **gevent** | [gevent/gevent](https://github.com/gevent/gevent) | Coroutine-based Python networking library that uses greenlets to provide a high-level synchronous API on top of the libev event loop, used here for efficient I/O-bound task processing in Celery workers. |
| **just** | [casey/just](https://github.com/casey/just) | Lightweight, cross-platform command runner that replaces complex shell scripts with clean, readable recipes, and natively loads environment variables directly from `.env` files out of the box (unlike `pixi tasks`). |
| **JupyterLab** *(Dev)* | [jupyterlab/jupyterlab](https://github.com/jupyterlab/jupyterlab) | Next-generation web-based interactive development environment for Jupyter notebooks; used here to create interactive documents for testing and verifying code execution. |

## Quick Start

### I. Prerequisites

| Component | Requirement |
| :--- | :--- |
| **Operating System** | GNU/Linux-based system |
| **Architecture** | `x86_64` |
| **Package Manager** | [`Pixi`](https://pixi.sh/latest/) |
| **Container Tools** | [Docker and Docker Compose](https://docs.docker.com/engine/install/) |

> **Note:**  
> These are not strict requirements, but rather the baseline environment where the code is 
> guaranteed to run out of the box. Alternative setups are possible but may require manual 
> configuration.

### II. Initial Setup

1. **Clone the repository**

    ```bash
    git clone https://github.com/snegoviol/celery-core.git
    cd celery-core
    ```

2. **Install dependencies**

    ```bash
    pixi install --all
    ```

3. **Activate Pixi environment**

    ```bash
    pixi shell
    ```

4. **Setup environment configuration**

    ```bash
    just copy-env
    just make-x
    just gen-acl
    ```

5. **Start Redis database**

    ```bash
    just redis-up
    ```

6. **Start Celery workers** (each in a separate terminal)

    ```bash
    pixi run just worker-cpu
    ```

    ```bash
    pixi run just worker-io
    ```

    > **Note:**  
    > It's normal to see red error messages in the CPU-bound worker logs — tasks are designed to randomly fail to verify Celery's error handling behavior in the absence of retry logic. For the I/O-bound worker, failed tasks will automatically be retried — this is expected behavior.

### III. Testing

Once the broker and Celery workers are ready, you can run and test the Celery implementation with the interactive Jupyter notebook `playground-testing.ipynb`. It demonstrates all core functionality — including task dispatch via `delay`, `apply_async`, `apply`, and result retrieval with `get()`.

1. **Launch JupyterLab**

    ```bash
    pixi run -e test jupyter lab
    ```

2. **Test the Celery implementation**
    - JupyterLab should open automatically in your browser at the default address.
    - Open the notebook and run the cells interactively.

3. **(Optional) Verify database manually**  
    You can open a Redis shell to manually verify that everything is working correctly:

    ```bash
    just redis-shell
    ```

### IV. Cleanup

When you finish testing:

1. **Stop JupyterLab**  
    In the terminal where JupyterLab is running, press `Ctrl+C` to shut it down.

2. **Stop Celery workers**  
    In each terminal where a Celery worker is running, press `Ctrl+C` to shut it down.

3. **Stop Redis**

    ```bash
    just redis-down
    ```

## License

Each file in this repository is licensed under the [MIT No Attribution License](LICENSE.txt).
