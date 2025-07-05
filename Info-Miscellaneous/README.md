
# Info_Miscellaneous

- **Deduplication** refers to a method of eliminating a dataset's redundant data.
- A **race condition** is an undesirable situation that occurs when a device or system attempts to perform two or more operations at the same time, but because of the nature of the device or system, the operations must be done in the proper sequence to be done correctly.
- **Persistence** denotes a process or an object that continues to exist even after its parent process or object ceases, or the system that runs it is turned off. When a created process needs persistence, non-volatile storage, a hard disk is used instead of volatile memory like RAM.
- **Medallion Architecture** is a data design pattern used to logically organize data in a lakehouse, with the goal of incrementally and progressively improving the structure and quality of data as it flows through each layer of the architecture (from Bronze ⇒ Silver ⇒ Gold layer tables). Medallion architectures are sometimes also referred to as "multi-hop" architectures. [Medallion Architecture _al](https://www.databricks.com/glossary/medallion-architecture)
- A **namespace** is a declarative region that provides a scope to the identifiers (the names of types, functions, variables, etc) inside it.
- [To setup env variables in a jupyternotebook _al](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-env): `%env var=val`
-  ['b' character do in front of a string literal _al](https://stackoverflow.com/questions/6269765/what-does-the-b-character-do-in-front-of-a-string-literal): A sequence of bytes. A “byte” is the smallest integer type addressable on a computer, which is nearly universally an octet, or 8-bit unit, thus allowing numbers between 0 and 255.
- The only thing that happens when you run touch on an already existing file is that the file's access and modification timestamps are updated to the current time. Its contents are not lost or modified.
- Setting the shell argument in the `subprocess` library to a true value causes subprocess to spawn an intermediate shell process, and tell it to run the command. In other words, using an intermediate shell means that variables, glob patterns, and other special shell features in the command string are processed before the command is run. Executing shell commands that incorporate unsanitized input from an untrusted source makes a program vulnerable to shell injection, a serious security flaw which can result in arbitrary command execution. For this reason, the use of shell=True is strongly discouraged in cases where the command string is constructed from external input. Hence: 

```
Case 1: 
db_config_create_command = f'touch {file_path}'
result_file_created = subprocess.run(db_config_create_command, shell=True, capture_output=True, text=True) -- Not advisable 

Case 2: 
db_config_create_command = ['touch', f'{file_path}']
subprocess.run(db_config_create_command, shell=False) -- Fine 
```
- Pip python package manager: 
  - `!pip install -r requirements.txt --use-deprecated=legacy-resolver` - For pip dependency issue resolving. 
  - `pip list`: pip list shows all the installed packages.
  - `pip freeze`: pip freeze shows packages installed via pip (or pipenv if using that tool) command in a requirements format.
  - `pip show jsonschema | grep ^Required-by`: Lists down all packages dependent on jsonschema package.
  - `pip freeze > requirements.txt`: Creates a requirements txt file having all packages with versions we got as output from pip freeze.
  - A virtual environment created using pip does not encapsulate the entire operating system ecosystem. Instead, it isolates Python packages and dependencies within a specific directory, allowing you to manage and install packages independently of the system-wide Python installation.
- Semantic versioning (also known as SemVer) is a versioning system that has been on the rise over the last few years. SemVer is in the form of MajorVersion.MinorVersion.PatchVersion; Eg: 4.7.6. [Semantic version doc _al](https://semver.org/)
- In this code:

```
Code: execution_date = datetime.combine(execution_date.date(), datetime.min.time()) + timedelta(hours=18).
The provided code performs the following actions below:

execution_date.date(): It extracts the date component from the execution_date variable, effectively removing the time component and leaving you with just the date.
datetime.min.time(): This creates a new datetime object with the minimum possible time (00:00:00).
datetime.combine(date, time): This combines the date obtained in step 1 with the time obtained in step 2. Essentially, it sets the time component of the execution_date to 00:00:00, effectively resetting the time to midnight.
timedelta(hours=18): It adds a time delta of 18 hours to the resulting datetime object. This effectively sets the time to 18:00:00 (6:00 PM) on the same date.

So, the overall effect of the code is to take an input execution_date, strip off the time component, and set the time to exactly 6:00 PM (18:00:00) on the same date. The resulting datetime object represents 6:00 PM on the same day as the input execution_date.
```

- The way jupyter kernel works: The main benefit for most users of using the kernel is this workflow that comes from the decoupling model. You can continue to write and execute code while other code is executing. The Jupyter kernel architecture consists of several components that work together to execute code, manage the execution environment, and communicate with the frontend. 
  - The kernel process is a standalone process that runs in the background and executes the code that you write in your notebooks. The kernel process is responsible for running the code and returning the results to the frontend.
  - The kernel manager is a component that manages the lifecycle of the kernel process. It is responsible for starting, stopping, and restarting the kernel process as needed.
  - The kernel gateway is a web server that exposes the kernel's functionality over HTTP. The kernel gateway is used to connect the kernel to the frontend (i.e., the web-based interface that you use to interact with the notebook) over a network connection.
  - The communication between the frontend and the kernel is done using WebSockets and ZeroMQ.

- pip installable parquet-tools - with this package you can view parquet file content/schema on local disk.
- Parquet is a columnar storage format, Avro is row-based. [parquet vs avro _vl](https://www.youtube.com/watch?v=QEjDiIyjFGs), [parquet format pros cons _al](https://stackoverflow.com/questions/36822224/what-are-the-pros-and-cons-of-the-apache-parquet-format-compared-to-other-format)
- API pagination refers to a technique used in API design and development to retrieve large data sets in a structured and manageable manner. When an API endpoint returns a large amount of data, pagination allows the data to be divided into smaller, more manageable chunks or pages. Pagination methods: Offset Pagination, Keyset Pagination, Seek Pagination
- Swagger io is a good option to send API contracts to foreign/ other teams: https://swagger.io/ 
- To print response time of hitting an API in requests library python: 
  - Python Requests library does all the TCP thing under the hood, a lot of things is abstracted out. Similar, happens with other libraries out there. 

```
response = requests.post(url, data=post_fields, timeout=timeout)
print(response.elapsed.total_seconds())
```

- Unix time is a date and time representation widely used in computing. It measures time by the number of non-leap seconds that have elapsed since 00:00:00 UTC on 1 January 1970, the Unix epoch. [Convert date to epoch python _al](https://stackoverflow.com/questions/75158409/how-to-convert-a-date-to-unix-epoch-time-in-python)
- [Why is processing a sorted array faster than processing an unsorted array _al](https://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-processing-an-unsorted-array): It involves concept of Branch prediction. Branch prediction attempts to guess whether a conditional jump will be taken or not. Branch target prediction attempts to guess the target of a taken conditional or unconditional jump before it is computed by decoding and executing the instruction itself. It is a technique to predict the outcome of a conditional operation.
- Multhread crawler in python using [bs4 - beautiful soup package _al](https://www.geeksforgeeks.org/multithreaded-crawler-in-python/). 
- What is P99 latency?: It's 99th percentile. It means that 99% of the requests should be faster than given latency. In other words only 1% of the requests are allowed to be slower. 
- Time-to-live (TTL) is a value for the period of time that a packet, or data, should exist on a computer or network before being discarded.
- Lazy loading is the practice of delaying load or initialization of resources or objects until they’re actually needed to improve performance and save system resources. For example, if a web page has an image that the user has to scroll down to see, you can display a placeholder and lazy load the full image only when the user arrives to its location -  with this you can reduce load time, conserve resources, etc. 
- TOAST in postgres: TOAST (The Oversized-Attribute Storage Technique) is a mechanism in Postgres which stores large column values in multiple physical rows, circumventing the page size limit of 8 KB.
- DRY Principal in programming: Do not repeat yourself 
- [Opensearch shard indexing backpressure](https://opensearch.org/blog/shard-indexing-backpressure-in-opensearch)
- Event reactor pattern: The reactor software design pattern is an event handling strategy that can respond to many potential service requests concurrently. The pattern's key component is an event loop, running in a single thread or process, which demultiplexes incoming requests and dispatches them to the correct request handler.
- [Aqua trivy _al](https://github.com/aquasecurity/trivy) is a useful tool to find vulnerabilities.
- [Pydantic validators _al](https://www.apptension.com/blog-posts/pydantic)
- A language is:
  - **statically typed** if the type of a variable is known at compile time, eg: int a = 5. For some languages this means that you as the programmer must specify what type each variable is; other languages (e.g.: Java, C, C++) offer some form of type inference, the capability of the type system to deduce the type of a variable (e.g.: OCaml, Haskell, Scala, Kotlin). Examples: C, C++, Java, Rust, Go, Scala
  - **dynamically typed** if the type is associated with run-time values, and not named variables/fields/etc. This means that you as a programmer can write a little quicker because you do not have to specify types every time (unless using a statically-typed language with type inference). Examples: Perl, Ruby, Python, PHP, JavaScript, Erlang
- Declarative vs Imperative programming: 
  - Declarative: You set the conditions that trigger the program execution to produce the desired results.

```
Eg: Declarative

mylist = [1,2,3,4,5]
total = sum(mylist)
print(total) 
```

  - Imperative: You describe the step-by-step instructions for how an executed program achieves the desired results.

```
Eg: Imperative

total = 0 
myList = [1,2,3,4,5]
for x in myList:
     total += x
print(total)
```

- TIL that if a function has a lot of arguments it takes a small hit on the call because the stack is used for the arguments instead of CPU registers. Depending on the OS (specifically 64 bit for some reason) anything > 4 goes into the stack. else it goes into the R registers.
- You know I used to think that 99.99% CPU at times or 98% RAM indicate bottlenecks and starved processes but that is not always the case. Your might have a multi-threaded or multi-process backend app that use 98% CPU of all cores at times but all processes are served equally and without any blocking or minimum blocking. You simply crafted the number of processes or threads in your CPU-bound backend workload so it runs efficient, which is what you want. So CPU usage alone isn’t enough to indicate pressure or stalling or blocking. I don’t know about Windows but in Linux, The psi (pressure stall information) is a metric I recently learned that tells you whether “some” or “full” pressure is being experienced, that is processes are being stalled for CPU or RAM. This even applies to RAM, some relational and in-memory databases pre-allocates large memory even though they are not using it showing large RAM usage but it doesn’t indicate that you necessarily need more RAM. SQL Server and memcachd comes to mind. So just because your memory usage is 98% doesn’t mean you necessarily need more RAM.
- What is the Torn Page in SQL Server? It is the inability of the server to fetch a particular data during a transaction. It is caused when an Input/Output header tries to access a page that was written incorrectly to the disk. It reports a message saying 'I/O error (torn page) detected during read'.
- [Backend for frontend pattern _al](https://medium.com/mobilepeople/backend-for-frontend-pattern-why-you-need-to-know-it-46f94ce420b0): You need to think of the user-facing application as being two components — a client-side application living outside your perimeter and a server-side component (BFF) inside your perimeter. BFF is a variant of the API Gateway pattern, but it also provides an additional layer between microservices and each client type separately. Instead of a single point of entry, it introduces multiple gateways. Because of that, you can have a tailored API that targets the needs of each client (mobile, web, desktop, voice assistant, etc.), and remove a lot of the bloat caused by keeping it all in one place. 
- An API gateway manages incoming requests and routes them based on key factors such as request path, headers, and query parameters, among others. It allows for efficient distribution of traffic and ensures proper load balancing among target endpoints.
- [Improve API performance _vl](https://www.youtube.com/watch?v=zvWKqUiovAM): Caching, Connection pool, Avoid N+1 Query Problem, Pagination, JSON Serializers, Payload Compression, Asynchronous logging
- **Serialization** is the process of converting the state of an object into a form that can be persisted or transported. The complement of serialization is **deserialization**, which converts a stream into an object. Together, these processes allow data to be stored and transferred.
- [Stateful vs Stateless _al](https://www.redhat.com/en/topics/cloud-native-apps/stateful-vs-stateless): 
  - Stateful: Stateful applications and processes allow users to store, record, and return to already established information and processes over the internet. In stateful applications, the server keeps track of the state of each user session, and maintains information about the user's interactions and past requests. They can be returned to again and again, like online banking or email. They’re performed with the context of previous transactions and the current transaction may be affected by what happened during previous transactions. For these reasons, stateful apps use the same servers each time they process a request from a user. If a stateful transaction is interrupted, the context and history have been stored so you can more or less pick up where you left off. Stateful apps track things like window location, setting preferences, and recent activity. You can think of stateful transactions as an ongoing periodic conversation with the same person.
  - A stateless process or application, however, does not retain information about the user's previous interactions. There is no stored knowledge of or reference to past transactions. Each transaction is made as if from scratch for the first time. Stateless applications provide one service or function and use a content delivery network (CDN), web, or print servers to process these short-term requests. An example of a stateless transaction would be doing a search online to answer a question you’ve thought of. You type your question into a search engine and hit enter. If your transaction is interrupted or closed accidentally, you just start a new one. Think of stateless transactions as a vending machine: a single request and a response.
- Ephemeral storage, in the context of Kubernetes, is storage tied to the lifecycle of a pod, so when a pod finishes or is restarted, that storage is cleared out.
- A bastion host is a publicly facing server that acts as an entry-point to the system which is protected from the high-end firewall or located in a private server. These servers can only be accessible from the bastion hosts so this would reduce the attack surface area from the outside world. [Private server bastion host _al](https://towardsaws.com/ssh-into-the-private-server-through-bastion-host-f637aa5f5c17), [SSH Proxy bastion _al](https://www.redhat.com/sysadmin/ssh-proxy-bastion-proxyjump)
- Data Warehouses are used by managers, analysts, and other business end-users. Data Lake stores mostly raw unstructured and semi-structured data — telemetry, graphics, logs of user behavior, website metrics, and information systems, as well as other data with different storage formats. They are not yet suitable for daily analytics in BI systems but can be used by Data Scientists to test new business hypotheses using statistical algorithms and Machine Learning methods.
- An SSL certificate is a digital certificate that authenticates a website's identity and enables an encrypted connection.
- A jump server is an intermediary device responsible for funneling traffic through firewalls using a supervised secure channel.
- Port forwarding, also known as port management, allows remote servers and devices on the internet to be able to access devices that are on a private network. If you are not using port forwarding, only devices on that private internal network can have access to each other or your network. A port is a communication endpoint and is identified as a port number that is always associated with an IP address. Of course, the whole reason you have security on your network is to keep outside sources from penetrating your network. If you are not able to grant access to an outside source, chances are you have good security over your network. However, there are some times when you need to grant access to an outside source whether it be to your clients or business partners to your documents or other elements within your network. Port forwarding will help you do that.
- Firecracker is a virtualization technology that was built to enable multi-tenant workloads on a single server. With Firecracker, different function and container workloads can share the computing resources of a single server in a secure manner, making workloads and underlying infrastructure all the more efficient. Firecracker is a lightweight virtual machine monitor (VMM) that uses Linux kernel-based virtual machines (KVM) to provision and manage lightweight virtual machines (VMs), also known as microVMs. These microVMs combine the isolation and security offered by full virtualization solutions with the speed and density provided by container technology. Developers can run containers and function-based workloads in these isolated and high-performing microVMs, and serverless providers can run thousands of these microVMs more efficiently on physical servers. [Firecracker VMs _al](https://www.koyeb.com/blog/firecracker-microvms-lightweight-virtualization-for-containers-and-serverless-workloads)
- Sqoop is a tool designed for efficiently transferring bulk data between Apache Hadoop and structured datastores such as relational databases. Sqoop has two main functions: importing and exporting. Importing transfers structured data into HDFS; exporting moves this data from Hadoop to external databases in the cloud or on-premises. Importing involves Sqoop assessing the external database’s metadata before mapping it to Hadoop. Sqoop undergoes a similar process when exporting data; it parses the metadata before moving the actual data to the repository. [Sqoop basics _al](https://www.guru99.com/introduction-to-flume-and-sqoop.html)
- json.loads() method can be used to parse a valid JSON string and convert it into a Python Dictionary.
- [venv, pyvenv, pyenv, virtualenv, virtualenvwrapper, pipenv difference _al](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe?rq=1):
  - virtualenv is a very popular tool that creates isolated Python environments for Python libraries. It works by installing a bunch of files in a directory (eg: env/), and then modifying the PATH environment variable to prefix it with a custom bin directory (eg: env/bin/). An exact copy of the python or python3 binary is placed in this directory, but Python is programmed to look for libraries relative to its path first, in the environment directory. It's not part of Python's standard library, but is officially blessed by the PyPA (Python Packaging Authority). Once activated, you can install packages in the virtual environment using pip.
  - pyenv is used to isolate Python versions. For example, you may want to test your code against Python 2.7, 3.6, 3.7 and 3.8, so you'll need a way to switch between them. Once activated, it prefixes the PATH environment variable with ~/.pyenv/shims, where there are special files matching the Python commands (python, pip). These are not copies of the Python-shipped commands; they are special scripts that decide on the fly which version of Python to run based on the PYENV_VERSION environment variable, or the .python-version file, or the ~/.pyenv/version file. pyenv also makes the process of downloading and installing multiple Python versions easier, using the command pyenv install.
  - pyenv-virtualenv is a plugin for pyenv by the same author as pyenv, to allow you to use pyenv and virtualenv at the same time conveniently. However, if you're using Python 3.3 or later, pyenv-virtualenv will try to run python -m venv if it is available, instead of virtualenv. You can use virtualenv and pyenv together without pyenv-virtualenv, if you don't want the convenience features.
  - virtualenvwrapper is a set of extensions to virtualenv (see docs). It gives you commands like mkvirtualenv, lssitepackages, and especially workon for switching between different virtualenv directories. This tool is especially useful if you want multiple virtualenv directories.
  - pyenv-virtualenvwrapper is a plugin for pyenv by the same author as pyenv, to conveniently integrate virtualenvwrapper into pyenv.
  - pipenv aims to combine Pipfile, pip and virtualenv into one command on the command-line. The virtualenv directory typically gets placed in ~/.local/share/virtualenvs/XXX, with XXX being a hash of the path of the project directory. This is different from virtualenv, where the directory is typically in the current working directory. pipenv is meant to be used when developing Python applications (as opposed to libraries). There are alternatives to pipenv, such as poetry, which I won't list here since this question is only about the packages that are similarly named.
- We often see cURL commands to hit API endpoints in technical docs of any software. It is a command-line tool and library for making HTTP requests and working with URLs. It stands for "Client for URLs" and is often used for testing APIs, making HTTP requests, and performing various web-related tasks from the command line or within scripts. Usage: 
  - Quick Testing: curl provides a simple and convenient way to quickly test an API endpoint without the need for a full-fledged programming environment. You can test different HTTP methods, headers, query parameters, and more by constructing a curl command.
  - Command-Line Usability: Many developers are comfortable using the command line, and curl offers a way to interact with APIs without needing to write code. This makes it accessible to a wide range of users.
  - Examples in Documentation: API documentation often includes curl examples to demonstrate how to make API requests. These examples are generally concise and can be easily copied and modified for testing purposes.
  - Cross-Platform: curl is available on various operating systems, including Unix-like systems (Linux, macOS) and Windows, making it a versatile choice for developers working on different platforms.
  - HTTP Methods and Options: curl supports various HTTP methods (GET, POST, PUT, DELETE, etc.) and allows you to customize headers, query parameters, and request bodies. This flexibility is useful when exploring API capabilities.
  - Debugging: When encountering issues with an API request, you can use curl to inspect the raw request and response data, including headers, to diagnose problems.
  - Automation: While it's a command-line tool, curl can also be used within scripts or automation processes to interact with APIs programmatically.
  - Example of using curl to make a GET request: `curl -X POST -H "Content-Type: application/json" -d '{"key": "value"}' https://api.example.com/endpoint`
  - Note: To convert cURL to Python request, follow this link: [Execute curl command _al](https://stackoverflow.com/questions/25491090/how-to-use-python-to-execute-a-curl-command)
- Memgraph is a high performant graph database that is compatible with Neo4j while eliminating Neo4j complexity.
- * operator in Python is called: splat operator
- [How is vector<vector<int>> "heavier" than vector<pair<int,int>>? _al](https://stackoverflow.com/questions/73095254/how-is-vectorvectorint-heavier-than-vectorpairint-int):
  - Each vector is a single contiguous area of memory, dynamically allocated.
  - Each vector is a single contiguous area of memory, dynamically allocated. Let's say that you have 1000 values you'll be working with. 
  - std::vector<std::pair<int, int>>: This gets you a single, contiguous block of memory, for 2000 integers.
  - std::vector<std::vector<int>>: This gets you a single contiguous block of memory for 1000 vectors. Each one of those 1000 std::vectors gets you another contiguous block of memory for just two integers.
  - So, instead of one single contiguous block of memory, for this data structure, it will consist of 1001 blocks of memory scattered all over. You have no guarantees, whatsoever, that all those blocks of memory will be contiguous, one after another.
  - Each dynamic memory allocation comes at a cost. The cost is fairly small but it adds up very, very quickly. A single penny is easily ignored. A thousand pennies should be enough to get you a cup of coffee at Starbucks.
  - Furthermore, modern CPUs are very good at accessing contiguous blocks of memory. Iterating over a single contiguous block of memory to add up two thousand ints will be much, much faster than doing the same over a thousand disjointed sections of memory.
- [psycopg2 leaking memory after large query _al](https://stackoverflow.com/questions/17199113/psycopg2-leaking-memory-after-large-query): Instead of `cursor = conn.cursor()`, use `cursor = conn.cursor(name="my_cursor_name")`
- When we say data loaded to memory - means RAM, and loaded to disk - means harddisk. 
- To get the recent commit in git, below 2 ways could give the details: 
  - Eg: 
  ```
  way1: 
  last_merge_commit=$(git log --merges -n 1 --pretty=format:"%H")
  sha=$(echo $last_merge_commit)
  
  way2: 
  sha=`git rev-parse HEAD`
  ```
- In the context of programming, a literal refers to a fixed value that is directly represented in the code. Literals are used to represent different data types, such as numbers, strings, booleans, and more.
- Differences in way C/C++ and Java code executed?: 
  - C/C++ Code Execution:
    - Compilation: When you write C/C++ code, it is compiled by a compiler into machine code (also called native code). This machine code is specific to the architecture of the computer's CPU (e.g., x86, ARM). 
      - Understand that: Machine code compiled for one architecture cannot be executed directly on a different architecture. For example, a program compiled for an x86 CPU will not run on an ARM CPU because the instruction sets are different.
      - Compatibility Layers: Some systems provide compatibility layers that allow executables compiled for one OS to run on another, provided the underlying CPU architecture is the same. For example, Wine on Linux can run Windows applications compiled for x86.
      - Emulators: Emulation software can simulate one CPU architecture on another, allowing machine code compiled for one type of processor to run on a different type. For instance, QEMU can emulate various CPU architectures, enabling execution of machine code designed for ARM on an x86 processor, albeit with a performance penalty.
      - Cross-Compilation: If you need to run C/C++ code on multiple architectures, the typical approach is to use cross-compilation. These are compilers that run on one architecture but generate machine code for another. For example, you can use an x86 machine to cross-compile a program for ARM.
      - Using VMs and containers is another aspect.
    - Execution: This machine code is executed directly by the computer's processor. Because it is compiled to run on a specific hardware architecture, it is very efficient but also hardware-dependent. 
  - Java Code Execution:
    - Compilation: When you write Java code, it is compiled by the Java compiler (javac) into bytecode, ***which is a platform-independent intermediate representation. This bytecode is not specific to any particular CPU architecture***.
    - JVM (Java Virtual Machine): 
      - Interpretation: The JVM can execute bytecode by interpreting it, which means reading and executing each instruction one at a time. This is why the JVM is often referred to as an interpreter.
      - ***Just-In-Time Compilation (JIT)***: Modern JVMs also use a technique called Just-In-Time (JIT) compilation. During execution, the JVM can compile frequently executed bytecode into native machine code for the host CPU. This native code is then executed directly by the CPU, improving performance.
        - It helps improve the performance of interpreted languages by dynamically compiling parts of the bytecode into native machine code at runtime.
        - JIT compilation combines the benefits of both interpreted and compiled languages. It involves compiling bytecode into native machine code on the fly, during program execution. This allows for optimizations based on the actual runtime behavior of the application.
        - How it works: 
          - Bytecode Execution: When you run a Java program, the JVM starts by interpreting the bytecode. This means it reads and executes the bytecode instructions one at a time. 
          - Hotspot Detection: The JVM monitors the execution of the bytecode and identifies "hotspots" – parts of the code that are executed frequently. These hotspots are good candidates for JIT compilation because they can benefit the most from optimization. 
          - Compilation: Once a hotspot is identified, the JVM's JIT compiler compiles that part of the bytecode into native machine code. This machine code can be executed directly by the CPU, which is much faster than interpreting the bytecode. 
          - Optimization: The JIT compiler can apply various optimizations that are only possible because it knows the actual runtime behavior of the application. For example, it can inline functions, optimize loops, and eliminate dead code. 
          - Execution: After compilation, the JVM replaces the interpreted bytecode with the compiled native code. Subsequent executions of that code run much faster because they are now running as optimized native code.
      - Specialized Java Hardware: While the JVM is typically implemented as a software layer running on top of an operating system, there have been efforts to create hardware that can execute Java bytecode directly. These specialized chips are not common but theoretically possible.
      - The Java Virtual Machine (JVM) indeed adds an additional layer between your Java code and the underlying hardware, which may initially seem to make execution slower. However, the JVM provides several key benefits that outweigh this initial overhead: Platform Independence, Write Once-Run Anywhere, Security, Memory allocation and Garbage collection.
  - Hence; Hardware Dependence: C/C++ is directly compiled to machine code specific to the hardware, leading to efficient but hardware-dependent executables. Java is compiled to bytecode, which is hardware-independent, and relies on the JVM to handle execution.
  - JVM as an Interpreter: The JVM interprets bytecode, allowing Java to be platform-independent. It can also compile bytecode to native code at runtime for better performance.
  - Abstracted Execution: The JVM abstracts the underlying hardware and OS, providing a consistent execution environment across different platforms.
- Interpretation vs. Compilation?:
  - Interpreted Languages: Each line of code is read and executed one at a time by an interpreter. This can be slower because the interpretation process adds overhead.
  - Compiled Languages: The entire program is compiled into native machine code before execution, leading to faster runtime performance since the machine code runs directly on the CPU.
  - The Compiler and Interpreter, both have similar works to perform. Interpreters and Compilers convert the Source Code (HLL) to Machine Code (understandable by Computer).
  - A compiler translates code written in a high-level programming language into a lower-level language like assembly language, object code and machine code (binary 1 and 0 bits). It converts the code ahead of time before the program runs.
  - An interpreter translates the code line-by-line when the program is running. Interpreters, more often than not are smaller than compilers. 
  - Compilers can contain interpreters for optimization reasons like faster performance and smaller memory footprint.
  - 4 Types of Interpreters: Bytecode interpreter, Threaded code interpreter, Abstract syntax tree interpreter, Justin-in-time compilation. 
  - 10 Types of Compilers: Cross-compiler, Native compiler, Bootstrap compiler, Decompiler, Source-to-source compiler, Language rewriter, Bytecode compiler, Just-in-time compiler, AOT compilation, Assembler.
- Java vs JS?: 
  - Java is an OOP programming language while Java Script is an OOP scripting language. 
  - Java creates applications that run in a virtual machine or browser while JavaScript code is run on a browser only. 
  - Java is a statically typed language; JavaScript is dynamic.
  - Java is class-based; JavaScript is prototype-based.
  - Java constructors are special functions that can only be called at object creation; JavaScript "constructors" are just standard functions.
  - Java requires all non-block statements to end with a semicolon; JavaScript inserts semicolons at the ends of certain lines.
  - Java uses block-based scoping; JavaScript uses function-based scoping.
  - Java has an implicit this scope for non-static methods, and implicit class scope; JavaScript has implicit global scope.
- Scripting langs vs Programming langs: Scripting languages are generally interpreted. Programming languages are typically compiled. This means that scripting languages are executed directly by the interpreter, while programming languages are first translated into machine code by the compiler before being executed. Programming languages create exe file, scripting languages don't.
- [Vacuum vs Vacuum Full in PostgreSQL _al](https://www.linkedin.com/pulse/vacuum-vs-full-postgresql-neelesh-ranjan-srivastava-cexbc):
  - VACUUM (Regular):
    - Marks dead/deleted rows as reusable space | Does NOT reduce physical file size | Runs concurrently with other operations | Updates visibility maps and prevents transaction ID wraparound
    - Non-blocking, allows reads/writes in DB | Minimal Performance Impact | Lightweight Resource Usage | Storage space marked for reuse, file size unchanged
    - Best for Regular maintenance | Scheduled automation | Production environments with high availability needs
  - VACUUM FULL: 
    - Physically rewrites table files without dead tuples | Reduces actual file size on disk | Rebuilds all indexes | Requires exclusive table locks
    - Blocks all operations on db (exclusive lock) | High performance impact during operation | High I/O CPU Resource Usage | Reduces physical disk storage
    - Use only after major bulk deletions | Disk space reclamation | Maintenance windows only as there is downtime, use sparingly
- 
- 

----------------------------------------------------------------------


