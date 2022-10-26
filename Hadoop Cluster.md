

<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/@highlightjs/cdn-assets@10.7.1/styles/default.min.css">
  </head>
  <body>
    <h1>Hands-on lab on Hadoop Cluster (20 mins)</h1>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/IDSNlogo.png" width="200/">
    <h4>What is a Hadoop Cluster?</h4>
    <p>A Hadoop cluster is a collection of computers, known as nodes, that are networked together to perform parallel computations on big data sets. The Name node is the master node of the Hadoop Distributed File System (HDFS). It maintains the meta data of the files in the RAM for quick access. An actual Hadoop Cluster setup involves extensives resources which are not within the scope of this lab. In this lab, you will use dockerized hadoop to create a Hadoop Cluster which will have:</p>
    <ol>
      <li>Namenode</li>
      <li>Datanode</li>
      <li>Node Manager</li>
      <li>Resource manager</li>
      <li>Hadoop history server</li>
    </ol>
    <h2>Objectives</h2>
    <ul>
      <li>Run a dockerized Cluster Hadoop instance</li>
      <li>Create a file in the HDFS and view it on the GUI</li>
    </ul>
    <h1></h1>
    <h3>Set up Cluster Nodes Dockerized Hadoop</h3>
    <ol>
      <li>Start a new terminal</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/New_terminal.png">
    <ol start="2">
      <li>Clone the repository to your theia environment.</li>
    </ol>
    <pre><code class="hljs language-awk">git clone https:<span class="hljs-regexp">//gi</span>thub.com<span class="hljs-regexp">/ibm-developer-skills-network/</span>ooxwv-docker_hadoop.git
</code></pre>
    <p></p>
    <ol start="3">
      <li>Navigate to the docker-hadoop directory to build it.</li>
    </ol>
    <pre><code class="hljs language-bash"><span class="hljs-built_in">cd</span> ooxwv-docker_hadoop
</code></pre>
    <p></p>
    <ol start="4">
      <li>Compose the docker application.</li>
    </ol>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">docker-compose up -d</span>
</code></pre>
    <p></p>
    <blockquote>
      <p><strong>Compose</strong> is a tool for defining and running multi-container Docker applications. It uses the YAML file to configure the serives and enables us to create and start all the services from just one configurtation file.</p>
    </blockquote>
    <p>You will see that all the five containers are created and started.</p>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/all_containers.png" width="50%">
    <ol start="5">
      <li>Run the namenode as a mounted drive on bash.</li>
    </ol>
    <pre><code class="hljs language-awk">docker exec -it namenode <span class="hljs-regexp">/bin/</span>bash
</code></pre>
    <p></p>
    <ol start="6">
      <li>You will observe that the prompt changes as shown below.</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/root_prompt.png" style="border: solid 1px white;">
    <h1>Explore the hadoop environment</h1>
    <p>As you have learnt in the videos and reading thus far in the course, a Hadoop environment is configured by editing a set of configuration files:</p>
    <ul>
      <li>
        <p><strong>hadoop-env.sh</strong> Serves as a master file to configure YARN, HDFS, MapReduce, and Hadoop-related project settings.</p>
      </li>
      <li>
        <p><strong>core-site.xml</strong> Defines HDFS and Hadoop core properties</p>
      </li>
      <li>
        <p><strong>hdfs-site.xml</strong> Governs the location for storing node metadata, fsimage file and log file.</p>
      </li>
      <li>
        <p><strong>mapred-site-xml</strong> Lists the parameters for MapReduce configuration.</p>
      </li>
      <li>
        <p><strong>yarn-site.xml</strong> Defines settings relevant to YARN. It contains configurations for the Node Manager, Resource Manager, Containers, and Application Master.</p>
      </li>
    </ul>
    <p>For the docker image, these xml files have been configured already. You can see these in the directory <strong>/opt/hadoop-3.2.1/etc/hadoop/</strong> by running</p>
    <pre><code class="hljs language-awk">ls <span class="hljs-regexp">/opt/</span>hadoop-<span class="hljs-number">3.2</span>.<span class="hljs-number">1</span><span class="hljs-regexp">/etc/</span>hadoop/*.xml
</code></pre>
    <p></p>
    <h1></h1>
    <h3>Create a file in the HDFS</h3>
    <ol>
      <li>In the HDFS, create a directory structure named <code>user/root/input</code>.</li>
    </ol>
    <pre><code class="hljs language-awk">hdfs dfs -mkdir -p <span class="hljs-regexp">/user/</span>root/input
</code></pre>
    <p></p>
    <ol start="2">
      <li>Copy all the hadoop configuration xml files into the input directory.</li>
    </ol>
    <pre><code class="hljs language-awk">hdfs dfs -put <span class="hljs-variable">$HADOOP_HOME</span><span class="hljs-regexp">/etc/</span>hadoop<span class="hljs-regexp">/*.xml /u</span>ser<span class="hljs-regexp">/root/i</span>nput
</code></pre>
    <p></p>
    <ol start="3">
      <li>Create a <code>data.txt</code> file in the current directory.</li>
    </ol>
    <pre><code class="hljs language-awk">curl https:<span class="hljs-regexp">//</span>raw.githubusercontent.com<span class="hljs-regexp">/ibm-developer-skills-network/</span>ooxwv-docker_hadoop<span class="hljs-regexp">/master/</span>SampleMapReduce.txt --output data.txt 
</code></pre>
    <p></p>
    <ol start="4">
      <li>Copy the <code>data.txt</code> file into <code>/user/root</code>.</li>
    </ol>
    <pre><code class="hljs language-awk">hdfs dfs -put data.txt <span class="hljs-regexp">/user/</span>root/
</code></pre>
    <p></p>
    <ol start="5">
      <li>Check if the file has been copied into the HDFS by viewing its content.</li>
    </ol>
    <pre><code class="hljs language-awk">hdfs dfs -cat <span class="hljs-regexp">/user/</span>root/data.txt
</code></pre>
    <p></p>
    <h1></h1>
    <h3>View the HDFS</h3>
    <ol>
      <li>Click on the Skills Network button on the left, it will open the "Skills Network Toolbox". Then click the Other then Launch Application. From there you should be able to enter the port number as <code>9870</code> and launch.</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Launch_Application--new_IDE.png">
    <ol start="2">
      <li>This will open up the Graphical User Interface (GUI) of the Hadoop node. Click on <code>Utilities</code> <strong>-></strong> <code>Broswe the file system</code> to browse the files.</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/browse_filesystem.png">
    <ol start="3">
      <li>View the files in the directories that you have just created by clicking on <code>user</code> then <code>root</code>.</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/browse_directory.png">
    <ol start="4">
      <li>
        <p>Notice that the block size is 128 MB though the file size is actually much smaller. This is because the default block size used by HDFS is 128 MB.</p>
      </li>
      <li>
        <p>You can click on the file to check the file into. It gives you information about the file in terms of number of bytes, block id etc.,</p>
      </li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/file_info.png" width="50%">
    <h1></h1>
    <h3>Congratulations! You have:</h3>
    <ul>
      <li>Deployed Hadoop using Docker</li>
      <li>Created data in HDFS and viewed it on the GUI</li>
    </ul>
    <p><a class="twitter-share-button" href="https://twitter.com/intent/tweet?utm_medium=Exinfluencer&#x26;utm_source=Exinfluencer&#x26;utm_content=000026UJ&#x26;utm_term=10006555&#x26;utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMBD0225ENSkillsNetwork25716109-2022-01-01&#x26;text=I+just+learned+how+to+deploy+%23Hadoop+using+%23Docker%2C+load+data+into+%23HDFS%2C+as+part+of+the+introductory+Big+Data+with+Hadoop+and+Spark+course+by+IBM."><img src="https://abs.twimg.com/errors/logo46x38.png" target="_blank">Tweet and share your achievement!</a></p>
    <h2>Author(s)</h2>
    <p>Lavanya T S</p>
    <h2>Changelog</h2>
    <table>
      <thead>
        <tr>
          <th>Date</th>
          <th>Version</th>
          <th>Changed by</th>
          <th>Change Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>18-01-2022</td>
          <td>1.0</td>
          <td>Lavanya</td>
          <td>Created lab instructions for Hadoop Cluster</td>
        </tr>
        <tr>
          <td>01-09-2022</td>
          <td>1.1</td>
          <td>K Sundararajan</td>
          <td>Updated instructions for <code>Launch Application</code> as per new Theia IDE</td>
        </tr>
      </tbody>
    </table>
    
  </body>
</html>
