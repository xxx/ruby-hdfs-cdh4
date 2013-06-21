# ruby libhdfs client

### requirements
  - ruby    (1.9.2 <=)
  - java    (1.6 <=)
  - libhdfs (2.x)

### installation
```
gem install ruby-hdfs-cdh4
```

this gem provides defaults for installation on machines using cdh4 and the hadoop-libhdfs cloudera package, but the following environment variables are available for configuration.

  - HADOOP_ENV
  - JAVA_HOME
  - JAVA_LIB

### usage
to setup your classpath on cdh4 machines require `hdfs/classpath`, or see [classpath.rb](https://github.com/dallasmarlow/ruby-hdfs-cdh4/blob/master/lib/hdfs/classpath.rb) as an example.

```ruby
require 'hdfs/classpath'

# connecting to HDFS

dfs = Hadoop::DFS::FileSystem.new host: 'namenode.domain.tld', port: 8020

dfs.list_directory('/').select(&:is_directory?).first.name
 => 'hdfs://namenode.domain.tld:8020/hbase'

# using Ruby APIs to interact with HDFS files

IO.copy_stream File.open('/tmp/local_file', 'rb'),
               dfs.open('/tmp/remote_file', 'w', replication: 3)
 => 36986

# copying and moving files from one HDFS to another

another_dfs = Hadoop::DFS::FileSystem.new host: 'namenode2.domain.tld', port: 8020
dfs.copy '/tmp/remote_file', '/tmp/remote_file', another_dfs
 => true

another_dfs.move '/tmp/remote_file', '/tmp/another_remote_file', dfs
 => true

dfs.delete '/tmp/another_remote_file'
 => true

# HDFS file mode, owner, group, and replication modifications

dfs.chmod '/tmp/remote_file', 755
 => true

dfs.chown '/tmp/remote_file', 'hdfs'
 => true

dfs.chgrp '/tmp/remote_file', 'hdfs'
 => true

dfs.set_replication '/tmp/remote_file', 2
 => true

# copying files from local filesystem to HDFS natively

local_fs = Hadoop::DFS::FileSystem.new local: true

local_fs.copy '/etc/hosts', '/tmp/hosts', dfs
 => true
```
