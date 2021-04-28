---
# tasks file for DataNode
#
#
  - name: Install Python and pip
    package:
      name:
      - python-pip
      state: present

  - name: Installing Gdown
    pip: 
    name: gdown

  - name: Downloading Hadoop and Dependencies
    shell : gdown -q --id 1SzsGGHsthag_AD8QWhzk1pW2JEAsaItI -O  jdk-8u171.rpm

  - name: Downloading Hadoop 
    shell: gdown -q --id 1IWf3YvkVH9n7yWcapsGEv8Ahr16qu1nY -O  hadoop-1.2.1.rpm 
           
  - name: Installing Hadoop Dependencies
    shell: rpm -i  /home/ec2-user/jdk-8u171.rpm

  - name: Installing Hadoop
    shell: rpm -i /home/ec2-user/hadoop-1.2.1.rpm --force

  - name: Create Directory 
    file:
      path: /datanode
      state: directory
 
  - name: Configure core-site.xml
    lineinfile: 
      path: /etc/hadoop/core-site.xml
      insertafter: '^<configuration>'
      line: "<property>\n<name>fs.default.name</name>\n<value>hdfs://{{ tag_Name_NameNode[0] }}:9001</value>\n</property>"
    notify: Refresh

  - name: Configure hdfs-site.xml
    lineinfile:
      path: /etc/hadoop/hdfs-site.xml
      insertafter: '^<configuration>'
      line: "<property>\n<name>dfs.data.dir</name>\n<value>/datanode</value>\n</property>"
    notify: Refresh

  - name: Start Service 
    service: 
      name: hadoop
      state: started




