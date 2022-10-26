
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/@highlightjs/cdn-assets@10.7.1/styles/default.min.css">
  </head>
  <body>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/IDSNlogo.png" alt="image">
    </p>
    <h1>Apache Spark Monitoring and Debugging</h1>
    <p><strong>Estimated time needed:</strong> 30 minutes</p>
    <p>This lab will instruct you on how to monitor and debug a Spark application through the web UI.</p>
    <h2>Objectives</h2>
    <p>After completing this lab, you will be able to:</p>
    <ol>
      <li>Start a Spark Standalone Cluster and connect with the PySpark shell.</li>
      <li>Create a DataFrame and open the application web UI.</li>
      <li>Debug a runtime error by locating the failed task in the web UI.</li>
      <li>Run an SQL query to monitor, then scale up by adding another worker to the cluster.</li>
    </ol>
    <h1>Exercise 1 : Start a Spark Standalone Cluster</h1>
    <p>
      In this exercise, you will initialize a Spark Standalone Cluster with a Master and one Worker.
      Next, you will start a PySpark shell that connects to the cluster and open the Spark Application
      Web UI to monitor it. We will be using the Theia terminal to run commands and docker-based
      containers to launch the Spark processes.
    </p>
    <h3>Task A : Download Sample Data for Spark</h3>
    <ol>
      <li>
        <p>Open a Theia terminal by clicking on the menu item <code>Terminal -> New Terminal</code>.</p>
      </li>
      <li>
        <p>
          Use the following command to download the data set we will be using in this lab to the
          container running Spark.
        </p>
      </li>
    </ol>
    <pre><code class="hljs language-bash">wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/data/cars.csv
</code></pre>
    <p></p>
    <h1>Task B : Initialize the Cluster</h1>
    <ol>
      <li>Stop any previously running containers with the command:</li>
    </ol>
    <pre><code class="hljs language-bash"><span class="hljs-keyword">for</span> i <span class="hljs-keyword">in</span> `docker ps | awk <span class="hljs-string">'{print $1}'</span> | grep -v CONTAINER`; <span class="hljs-keyword">do</span> docker <span class="hljs-built_in">kill</span> <span class="hljs-variable">$i</span>; <span class="hljs-keyword">done</span>
</code></pre>
    <p></p>
    <ol start="2">
      <li>Remove any previously used containers:<br>Ignore any errors that say "No such container"</li>
    </ol>
    <pre><code class="hljs language-bash">docker rm spark-master spark-worker-1 spark-worker-2
</code></pre>
    <p></p>
    <ol start="3">
      <li>Start the Spark Master server:</li>
    </ol>
    <pre><code class="hljs language-bash">docker run \
    --name spark-master \
    -h spark-master \
    -e ENABLE_INIT_DAEMON=<span class="hljs-literal">false</span> \
    -p 4040:4040 \
    -p 8080:8080 \
    -v `<span class="hljs-built_in">pwd</span>`:/home/root \
    -d bde2020/spark-master:3.1.1-hadoop3.2
</code></pre>
    <p></p>
    <ol start="4">
      <li>Start a Spark Worker that will connect to the Master:</li>
    </ol>
    <pre><code class="hljs language-bash">docker run \
    --name spark-worker-1 \
    --link spark-master:spark-master \
    -e ENABLE_INIT_DAEMON=<span class="hljs-literal">false</span> \
    -p 8081:8081 \
    -v `<span class="hljs-built_in">pwd</span>`:/home/root \
    -d bde2020/spark-worker:3.1.1-hadoop3.2
</code></pre>
    <p></p>
    <h1>Task C : Connect a PySpark Shell to the Cluster and Open the UI</h1>
    <ol>
      <li>Launch a PySpark shell in the running Spark Master container:</li>
    </ol>
    <pre><code class="hljs language-bash">docker <span class="hljs-built_in">exec</span> \
    -it `docker ps | grep spark-master | awk <span class="hljs-string">'{print $1}'</span>` \
    /spark/bin/pyspark \
    --master spark://spark-master:7077
</code></pre>
    <p></p>
    <ol start="2">
      <li>Create a DataFrame in the shell with:</li>
    </ol>
    <pre><code class="hljs language-python">df = spark.read.csv(<span class="hljs-string">"/home/root/cars.csv"</span>, header=<span class="hljs-literal">True</span>, inferSchema=<span class="hljs-literal">True</span>) \
    .repartition(<span class="hljs-number">32</span>) \
    .cache()
df.show()
</code></pre>
    <p></p>
    <ol start="3">
      <li>Click on the Skills Network button on the left, it will open the "Skills Network Toolbox". Then click <code>OTHER</code> then <code>Launch Application</code>. From there you should be able to enter the port number as <code>4040</code> and launch the Spark Application UI in your browser.</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Launch_Application--new_IDE.png">
    <p><br><br></p>
    <ol start="4">
      <li>
        Verify you can see the application jobs page that should look like the following, although
        not necessarily exactly the same:
        
        <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/SparkUI-Initial-Page.png" alt="image">
      </li>
    </ol>
    <h1>Exercise 2 : Run an SQL Query and Debug in the Application UI</h1>
    <p>
      In this exercise, you will define a user-defined function (UDF) and run a query that results in an
      error. We will locate that error in the application UI and find the root cause. Finally, we will
      correct the error and re-run the query.
    </p>
    <h3>Task A : Run an SQL Query</h3>
    <ol>
      <li>Define a UDF to show engine type. Copy and paste the code and click <code>Enter</code>.</li>
    </ol>
    <pre><code class="hljs language-python"><span class="hljs-keyword">from</span> pyspark.sql.functions <span class="hljs-keyword">import</span> udf
<span class="hljs-keyword">import</span> time

<span class="hljs-meta">@udf(<span class="hljs-params"><span class="hljs-string">"string"</span></span>)</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">engine</span>(<span class="hljs-params">cylinders</span>):</span>
    time.sleep(<span class="hljs-number">0.2</span>)  <span class="hljs-comment"># Intentionally delay task</span>
    eng = {<span class="hljs-number">6</span>: <span class="hljs-string">"V6"</span>, <span class="hljs-number">8</span>: <span class="hljs-string">"V8"</span>}
    <span class="hljs-keyword">return</span> eng[cylinders]
</code></pre>
    <p></p>
    <ol start="2">
      <li>Add the UDF as a column in the DataFrame</li>
    </ol>
    <pre><code class="hljs language-python">df = df.withColumn(<span class="hljs-string">"engine"</span>, engine(<span class="hljs-string">"cylinders"</span>))
</code></pre>
    <p></p>
    <ol start="3">
      <li>Group the DataFrame by "cylinders" and aggregate other columns</li>
    </ol>
    <pre><code class="hljs language-python">dfg = df.groupby(<span class="hljs-string">"cylinders"</span>)
</code></pre>
    <p></p>
    <pre><code class="hljs language-python">dfa = dfg.agg({<span class="hljs-string">"mpg"</span>: <span class="hljs-string">"avg"</span>, <span class="hljs-string">"engine"</span>: <span class="hljs-string">"first"</span>})
</code></pre>
    <p></p>
    <pre><code class="hljs language-python">dfa.show()
</code></pre>
    <p></p>
    <ol start="4">
      <li>The query will have failed and you should see lots of messages and outputs in the console.</li>
    </ol>
    <p>The next task will be to locate the error in the Application UI and determine the root cause.</p>
    <h1>Task B : Debug the error in the Application UI</h1>
    <ol>
      <li>Find the error in the Application UI</li>
    </ol>
    <p>Open UI to the Jobs, look at list of Failed Jobs, click on first job.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Failure-Jobs-tab.png" alt="image">
    </p>
    <ol start="2">
      <li>
        This will bring up the Job
        details with a list of stages for that job. In the list of Failed Stages, click on the first
        failed stage to show the stage details with a list of tasks for that stage.
      </li>
    </ol>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Failure-Jobs-detail.png" alt="image">
    </p>
    <ol start="3">
      <li>
        Here we see lots
        of failed tasks. Looking at the first one, the far right column shows details of the failure.
      </li>
    </ol>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Failure-Stage-list.png" alt="image">
    </p>
    <p>Click to expand the details.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Failure-Task-details.png" alt="image">
    </p>
    <p>
      Scroll down a little until you can see the last part of the Python
      error that shows the cause. You should be able to see this was caused by a KeyError in our UDF
      <code>engine()</code>.
    </p>
    <p>
      You could also view these errors by looking at the column that has links to the logs and click on
      "std err" to show the standard error log.
    </p>
    <p>Close the PySpark browser tab.</p>
    <ol start="4">
      <li>In the Theia terminal, fix the UDF by adding an entry to the dictionary of engine types and provide a</li>
    </ol>
    <p>default for all other types. Copy and paste this code and click <code>Enter</code>.</p>
    <pre><code class="hljs language-python"><span class="hljs-meta">@udf(<span class="hljs-params"><span class="hljs-string">"string"</span></span>)</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">engine</span>(<span class="hljs-params">cylinders</span>):</span>
    time.sleep(<span class="hljs-number">0.2</span>)  <span class="hljs-comment"># Intentionally delay task</span>
    eng = {<span class="hljs-number">4</span>: <span class="hljs-string">"inline-four"</span>, <span class="hljs-number">6</span>: <span class="hljs-string">"V6"</span>, <span class="hljs-number">8</span>: <span class="hljs-string">"V8"</span>}
    <span class="hljs-keyword">return</span> eng.get(cylinders, <span class="hljs-string">"other"</span>)
</code></pre>
    <p></p>
    <ol start="5">
      <li>Re-run the query. You will have to add the "engine" column again and enter the query since</li>
    </ol>
    <p>we changed the UDF.</p>
    <pre><code class="hljs language-python">df = df.withColumn(<span class="hljs-string">"engine"</span>, engine(<span class="hljs-string">"cylinders"</span>))
</code></pre>
    <p></p>
    <pre><code class="hljs language-python">dfg = df.groupby(<span class="hljs-string">"cylinders"</span>)
</code></pre>
    <p></p>
    <pre><code class="hljs language-python">dfa = dfg.agg({<span class="hljs-string">"mpg"</span>: <span class="hljs-string">"avg"</span>, <span class="hljs-string">"engine"</span>: <span class="hljs-string">"first"</span>})
</code></pre>
    <p></p>
    <pre><code class="hljs language-python">dfa.show()
</code></pre>
    <p></p>
    <p>Once the query completes without errors, you should see output similar to this.</p>
    <pre><code class="hljs language-gherkin">+---------+------------------+-------------+                                    
|<span class="hljs-string">cylinders</span>|<span class="hljs-string">          avg(mpg)</span>|<span class="hljs-string">first(engine)</span>|
+---------+------------------+-------------+
|<span class="hljs-string">        6</span>|<span class="hljs-string">19.985714285714288</span>|<span class="hljs-string">           V6</span>|
|<span class="hljs-string">        3</span>|<span class="hljs-string">             20.55</span>|<span class="hljs-string">        other</span>|
|<span class="hljs-string">        5</span>|<span class="hljs-string">27.366666666666664</span>|<span class="hljs-string">        other</span>|
|<span class="hljs-string">        4</span>|<span class="hljs-string">29.286764705882348</span>|<span class="hljs-string">  inline-four</span>|
|<span class="hljs-string">        8</span>|<span class="hljs-string">14.963106796116506</span>|<span class="hljs-string">           V8</span>|
+---------+------------------+-------------+
</code></pre>
    <h1>Exercise 3 : Monitor Application Performance with the UI</h1>
    <p>
      Now that we have run our query successfully, we will scale up our application by adding a worker
      to the cluster. This will allow the cluster to run more tasks in parallel and improve the
      overall performance.
    </p>
    <h3>Task A : Add a Worker to the Cluster</h3>
    <ol>
      <li>View the Stages tab, then</li>
    </ol>
    <p>
      click on the stage with 32 tasks. In that stage our UDF is being applied to each partition of the
      DataFrame.
    </p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Perf-stage-udf.png" alt="image">
    </p>
    <p>
      Looking at the timeline, you can see there is a single worker with id <code>0 / &#x3C;ip-address></code> that can
      run up to a certain amount of tasks in parallel at one time. Adding another worker will allow an
      additional tasks to be run in parallel.
    </p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Perf-parallel-tasks-1.png" alt="image">
    </p>
    <ol start="2">
      <li>
        <p>Open a new Theia terminal by clicking on the menu item <code>Terminal -> New Terminal</code>.</p>
      </li>
      <li>
        <p>Add a second worker to the cluster with the command in the new terminal:</p>
      </li>
    </ol>
    <pre><code class="hljs language-bash">docker run \
    --name spark-worker-2 \
    --link spark-master:spark-master \
    -e ENABLE_INIT_DAEMON=<span class="hljs-literal">false</span> \
    -p 8082:8082 \
    -d bde2020/spark-worker:3.1.1-hadoop3.2
</code></pre>
    <p></p>
    <ol start="4">
      <li>If the command is successful, there will be a single output showing the container id:</li>
    </ol>
    <pre><code class="hljs language-node-repl">theia@theiadocker-user:/home/project$ docker run \
<span class="hljs-meta">></span> <span class="javascript">    --name spark-worker-<span class="hljs-number">2</span> \</span>
<span class="hljs-meta">></span> <span class="javascript">    --link spark-master:spark-master \</span>
<span class="hljs-meta">></span> <span class="javascript">    -e ENABLE_INIT_DAEMON=<span class="hljs-literal">false</span> \</span>
<span class="hljs-meta">></span> <span class="javascript">    -p <span class="hljs-number">8082</span>:<span class="hljs-number">8082</span> \</span>
<span class="hljs-meta">></span> <span class="javascript">    -d bde2020/spark-worker:<span class="hljs-number">3.1</span><span class="hljs-number">.1</span>-hadoop3<span class="hljs-number">.2</span></span>
1935a71827668ae3476e6a16f0bebcd4c2a342a21271dc22be487aa1b1731708
theia@theiadocker-user:/home/project$
</code></pre>
    <p></p>
    <ol start="5">
      <li>Click back to the first terminal that has the PySpark shell open to continue.</li>
    </ol>
    <h1>Task B : Re-run the query and check performance</h1>
    <ol>
      <li>Re-run the query, this time we can simply call <code>show()</code> again:</li>
    </ol>
    <pre><code class="hljs language-python">dfa.show()
</code></pre>
    <p></p>
    <ol start="2">
      <li>Launch Application on port number <code>4040</code> by following the same process as above, to open the PySpark browser.</li>
    </ol>
    <p>
      Go to the <strong>Stages</strong> tab and see the most recent stage Id.
      
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/RecentStageView.png" alt="image">
    </p>
    <ol start="3">
      <li>You will see that the additional worker with id <code>1 / &#x3C;ip-address></code> is listed and now allows more tasks</li>
    </ol>
    <p>to be run in parallel. The task timeline should look similar to the following.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/Perf-parallel-tasks-2.png" alt="image">
    </p>
    <h2>Author(s)</h2>
    <p>Aije</p>
    <h3>Other Contributor(s)</h3>
    <p>Lavanya</p>
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
          <td>2021-07-16</td>
          <td>0.1</td>
          <td>Aije</td>
          <td>Initial version created</td>
        </tr>
        <tr>
          <td>2022-01-03</td>
          <td>0.2</td>
          <td>Lavanya</td>
          <td>Changed the instructions for second node</td>
        </tr>
        <tr>
          <td>2022-09-01</td>
          <td>0.3</td>
          <td>K Sundararajan</td>
          <td>Updated instructions for <code>Launch Application</code> as per new Theia IDE</td>
        </tr>
      </tbody>
    </table>
    <h2></h2>
    <h3 align="center">© IBM Corporation 2021. All rights reserved.</h3>
    <h3></h3>
    
  </body>
</html>
