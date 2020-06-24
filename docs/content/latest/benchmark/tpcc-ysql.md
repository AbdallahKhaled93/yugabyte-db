---
title: Benchmark YSQL performance using TPC-C
headerTitle: TPC-C
linkTitle: TPC-C
description: Benchmark YSQL performance using TPC-C
headcontent: Benchmark YugabyteDB using TPC-C
menu:
  latest:
    identifier: tpcc-ysql
    parent: benchmark
    weight: 4
aliases:
  - /benchmark/tpcc/
showAsideToc: true
isTocNested: true
---

<ul class="nav nav-tabs-alt nav-tabs-yb">

  <li >
    <a href="/latest/benchmark/tpcc-ysql/" class="nav-link active">
      <i class="icon-postgres" aria-hidden="true"></i>
      YSQL
    </a>
  </li>

</ul>

## Overview
Follow the steps below to run the open-source [oltpbench](https://github.com/oltpbenchmark/oltpbench) TPC-C workload against YugabyteDB YSQL. [TPC-C](http://www.tpc.org/tpcc/) is a popular online transaction processing benchmark that provides metrics you can use to evaluate the performance of YugabyteDB for concurrent transactions of different types and complexity that are either either executed online or queued for deferred execution.

## Running the benchmark

### 1. Prerequisites

To download the TPC-C binaries, run the following commands.

```sh
$ cd $HOME
$ wget https://github.com/yugabyte/tpcc/releases/download/1.1/tpcc.tar.gz
$ tar -zxvf tpcc.tar.gz
$ cd tpcc
```

{{< note title="Note" >}}
The binaries are compiled with JAVA 13 and it is recommended to run these binaries with that version.
{{< /note >}}

### Step 2. Start your database

Start your YugabyteDB cluster by following the steps [here](../../deploy/manual-deployment/).

{{< tip title="Tip" >}}
You will need the IP addresses of the nodes in the cluster for the next step.
{{< /tip>}}

### Step 3. Running the TPC-C benchmark

Use the provided utility script (`./tpccbenchmark`) to run the TPC-C benchmark.

Before starting the workload, you will need to load the data first.

```sh
$ ./tpccbenchmark --create=true --load=true
```

The above command runs the benchmark against the database running at 127.0.0.1 with 10 warehouses.
To run the benchmark against a specific set of nodes with 100 warehouses:

```sh
$ ./tpccbenchmark --create=true --load=true \
  --nodes=127.0.0.1,127.0.0.2,127.0.0.3 --warehouses=100 --loaderthreads=48
```

You can then run the workload.

```sh
$ ./tpccbenchmark --execute=true -o outputfile
```
or

```sh
$ ./tpccbenchmark --execute=true -o outputfile \
  --nodes=127.0.0.1,127.0.0.2,127.0.0.3 --warehouses=100 -o outputfile
```

You can also load and run the benchmark in a single step:

```sh
$ ./tpccbenchmark --create=true --load=true --execute=true -o outputfile \
  --nodes=127.0.0.1,127.0.0.2,127.0.0.3 --warehouses=100 -o outputfile
```

## Output

Once the execution is done the TPM-C number along with the efficiency is printed.

```
23:52:44,383 (DBWorkload.java:880) INFO  - Rate limited reqs/s: Results(nanoSeconds=500000707198, measuredRequests=23616) = 47.23193319534262 requests/sec
23:52:44,383 (DBWorkload.java:885) INFO  - Num New Order transactions : 10376, time seconds: 500
23:52:44,383 (DBWorkload.java:886) INFO  - TPM-C: 1245
23:52:44,383 (DBWorkload.java:887) INFO  - Efficiency : 96.81181959564542
23:52:44,384 (DBWorkload.java:722) INFO  - Output Raw data into file: results/output.5.csv
```

## Tweaking the configuration

We can change the default username, password, port, etc. using the configuration file at `config/workload_all.xml`. We can also change the terminals or the physical connections being used by the benchmark using the configuration.
```sh
<!-- Connection details -->
<dbtype>postgres</dbtype>
<driver>org.postgresql.Driver</driver>
<port>5433</5433>
<username>yugabyte</username>
<password></password>
<isolation>TRANSACTION_REPEATABLE_READ</isolation>

<terminals>100</terminals>
<numDBConnections>10</numDBConnections>
```

{{< note title="Note" >}}
By default the number of terminals is 10 times the number of warehouses which is the max that the TPC-C spec allows. The number of DB connections is the same as the number of warehouses.
{{< /note >}}
