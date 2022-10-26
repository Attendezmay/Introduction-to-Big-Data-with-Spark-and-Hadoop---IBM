
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/@highlightjs/cdn-assets@10.7.1/styles/default.min.css">
  </head>
  <body>
    <h1>Hands-on lab on Hadoop Map-Reduce (20 mins)</h1>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/IDSNlogo.png" width="200/">
    <h4>Objectives</h4>
    <ul>
      <li>Run a single-node Hadoop instance</li>
      <li>Perform a word count using Hadoop <strong>Map Reduce</strong>.</li>
    </ul>
    <h1>Set up Single-Node Hadoop</h1>
    <p>The steps outlined in this lab use the single-node Hadoop Version 3.2.3. <strong>Hadoop</strong> is most useful when deployed in a fully distributed mode on a large cluster of networked servers sharing a large volume of data. However, for basic understanding, we will configure Hadoop on a single node.</p>
    <p>In this lab, we will run the WordCount example with an input text and see how the content of the input file is processed by WordCount.</p>
    <ol>
      <li>Start a new terminal</li>
    </ol>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/New_terminal.png">
    <ol start="2">
      <li>Download hadoop-3.2.3.tar.gz to your theia environment by running the following command.</li>
    </ol>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">curl</span> https://dlcdn.apache.org/hadoop/common/hadoop-<span class="hljs-number">3</span>.<span class="hljs-number">2</span>.<span class="hljs-number">3</span>/hadoop-<span class="hljs-number">3</span>.<span class="hljs-number">2</span>.<span class="hljs-number">3</span>.tar.gz --output hadoop-<span class="hljs-number">3</span>.<span class="hljs-number">2</span>.<span class="hljs-number">3</span>.tar.gz
</code></pre>
    <p></p>
    <ol start="3">
      <li>Extract the tar file in the currently directory.</li>
    </ol>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">tar</span> -xvf hadoop-<span class="hljs-number">3</span>.<span class="hljs-number">2</span>.<span class="hljs-number">3</span>.tar.gz
</code></pre>
    <p></p>
    <ol start="4">
      <li>Navigate to the hadoop-3.2.3 directory.</li>
    </ol>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cd</span> hadoop-<span class="hljs-number">3</span>.<span class="hljs-number">2</span>.<span class="hljs-number">3</span>
</code></pre>
    <p></p>
    <ol start="5">
      <li>Check the hadoop command to see if it is setup. This will display the usage documentation for the hadoop script.</li>
    </ol>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">bin</span>/hadoop
</code></pre>
    <p></p>
    <ol start="6">
      <li>Run the following command to download data.txt to your current directory.</li>
    </ol>
    <pre><code class="hljs language-awk">curl https:<span class="hljs-regexp">//</span>cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud<span class="hljs-regexp">/IBM-BD0225EN-SkillsNetwork/</span>labs<span class="hljs-regexp">/data/</span>data.txt --output data.txt
</code></pre>
    <p></p>
    <ol start="7">
      <li>Run the Map reduce application for wordcount on data.txt and store the output in <strong>/user/root/output</strong></li>
    </ol>
    <pre><code class="hljs language-awk">bin<span class="hljs-regexp">/hadoop jar share/</span>hadoop<span class="hljs-regexp">/mapreduce/</span>hadoop-mapreduce-examples-<span class="hljs-number">3.2</span>.<span class="hljs-number">3</span>.jar wordcount data.txt output
</code></pre>
    <p></p>
    <blockquote>
      <p>This may take some time.</p>
    </blockquote>
    <ol start="8">
      <li>Once the word count runs successfully, you can run the following command to see the output file it has generated.</li>
    </ol>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">ls output</span>
</code></pre>
    <p></p>
    <p>You should see <strong>part-r-00000</strong> with <strong>_SUCCESS</strong> indicating that the wordcount has been done.</p>
    <blockquote>
      <p>While it is still processing, you may only see <em>'_temporary'</em> listed in the output directory. Wait for a couple of minutes and run the command again till you see output as shown above.</p>
    </blockquote>
    <ol start="9">
      <li>Run the following command to see the word count output.</li>
    </ol>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cat</span>  output/part-r-<span class="hljs-number">00000</span>
</code></pre>
    <p></p>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/wordcount_output.png" alt="Local wordcount output">
    <p>The image below shows how the MapReduce wordcount happens.</p>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/map_reduce_picture_rep.png">
    <h1>Practice Lab</h1>
    <ol>
      <li>Do a word count on a file with the following content.</li>
    </ol>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">Italy Venice
Italy Pizza
Pizza Pasta Gelato</span>

</code></pre>
    <details>
      <summary>Click here for a hint on how to get started</summary>- Delete the data.txt file and output folder
      <pre><code class="hljs language-haskell"><span class="hljs-title">rm</span> <span class="hljs-class"><span class="hljs-keyword">data</span>.txt</span>
</code></pre>
      <p></p>
      <pre><code class="hljs language-ebnf"><span class="hljs-attribute">rm -rf output</span>
</code></pre>
      <p></p>
    </details>
    <details>
      <summary>Click here for hint on how to create a file to wordcount</summary>Create data.txt with the required content. You may either use the file editor.
    </details>
    <details>
      <summary>Click here for solution on how to do word count on the file</summary>Run the following command
      <pre><code class="hljs language-awk">bin<span class="hljs-regexp">/hadoop jar share/</span>hadoop<span class="hljs-regexp">/mapreduce/</span>hadoop-mapreduce-examples-<span class="hljs-number">3.2</span>.<span class="hljs-number">3</span>.jar wordcount data.txt output
</code></pre>
    </details>
    <details>
      <summary>Click here for sample output</summary>
      <p>The output will be as below.</p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-BD0225EN-SkillsNetwork/labs/images/practice_lab_output.png" style="border: solid 1px white; margin-top:10px; margin-bottom:10px">
    </details>
    <h1>Congratulations! You have:</h1>
    <ul>
      <li>Deployed Hadoop using Docker</li>
      <li>Copied data into HDFS</li>
      <li>Used MapReduce to do a word count</li>
    </ul>
    <p><a class="twitter-share-button" href="https://twitter.com/intent/tweet?utm_medium=Exinfluencer&#x26;utm_source=Exinfluencer&#x26;utm_content=000026UJ&#x26;utm_term=10006555&#x26;utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMBD0225ENSkillsNetwork25716109-2022-01-01&#x26;text=I+just+learned+how+to+deploy+%23Hadoop+using+%23Docker%2C+load+data+into+%23HDFS%2C+and+used+%23MapReduce+to+perform+a+word+count+as+part+of+the+introductory+Big+Data+with+Hadoop+and+Spark+course+by+IBM."><img src="https://abs.twimg.com/errors/logo46x38.png">Tweet and share your achievement!</a></p>
    <h2>Author(s)</h2>
    <p>Lavanya T S</p>
    <h2>Contributor(s)</h2>
    <p><a href="https://www.linkedin.com/in/aije-egwaikhide/?utm_medium=Exinfluencer&#x26;utm_source=Exinfluencer&#x26;utm_content=000026UJ&#x26;utm_term=10006555&#x26;utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMBD0225ENSkillsNetwork25716109-2022-01-01" target="_blank" rel="external">Aije Egwaikhide</a></p>
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
          <td>05-04-2022</td>
          <td>1.3</td>
          <td>Sourabh</td>
          <td>Updated Hadoop version</td>
        </tr>
        <tr>
          <td>18-01-2022</td>
          <td>1.2</td>
          <td>Lavanya</td>
          <td>Changed to single node hadoop</td>
        </tr>
        <tr>
          <td>16-07-2021</td>
          <td>1.1</td>
          <td>Aije</td>
          <td>Modified multiple areas</td>
        </tr>
        <tr>
          <td>11-07-2021</td>
          <td>1.0</td>
          <td>Lavanya</td>
          <td>Created lab instructions for Word count using MapReduce</td>
        </tr>
      </tbody>
    </table>
    <script>window.addEventListener('load', function() 
  </body>
</html>
