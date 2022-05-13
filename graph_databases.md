## 🎓🔥 Graph Database practice

This is an optional part that is normally left as an exercise for the reader,
to offer a hands-on learning experience on Graph Databases using DataStax Graph
and DataStax Studio.

> Note: as opposed to the rest of today's pratice, this needs to be done on your own machine.

**✅ 5a. Prerequisites**

**Minimal Configuration**: You need to have a computer with this minimal configuration requirements
- At least 2CPU
- At least 6GB or RAM

**Install Docker and Docker Compose**

You need to install Docker and Docker-compose on your machine
- [Install **Docker** for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#1-install-docker)
- [Install **Docker-Compose**  for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#2-install-docker-compose)

**✅ 5b. Create a docker network named 'graph'**

```bash
docker network create graph
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker network create graph

64f8bcc2dda416d6dc80ef3c1ac97902b9d90007842808308e9d741d179d9344
```

**✅ 5c.Clone this repository (or download ZIP from the github UI)**

```bash
git clone https://github.com/datastaxdevs/workshop-introduction-to-nosql.git

cd workshop-introduction-to-nosql
```

**✅ 5d.Start the containers**

:warning: *Linux users:*
  >Folder `datastax-studio-config` is mapped to docker container (see: `docker-compose.yaml` file) and dse studio runs as user `studio` with `uid=997` and         >`gui=997` which needs RW access to that folder.
  >
  >Run this command if you are on a linux system:
  >```bash
  >sudo chown -R 997:997 ./datastax-studio-config
  >```
:warning: *Linux users:*

:📝 Note for *Windows users:*
  >Start the *studio image* `without a volume`. Remove these 2 lines above `networks` in *studio* (see: `docker-compose.yaml` file) 
  >```yaml
  >volumes:
  >    - "./datastax-studio-config:/var/lib/datastax-studio"

:📝 Note for *Windows users:*

Start containers:
```bash
docker-compose up -d
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose up -d

Creating dse ... done
Creating workshop-introduction-to-nosql_studio_1 ... done
```
Wait for the application to start (30s) and open [http://localhost:9091](http://localhost:9091)


![image](images/studio_home.png?raw=true)

**✅ 5e.Check database connection**

Open the ellipsis and click `Connections`

![image](images/studio_test_connection1.png?raw=true)

Select the `default localhost` connection

![image](images/studio_test_connection2.png?raw=true)

Check that `dse` is set for the host (pointing to a local cassandra)

![image](images/studio_test_connection3.png?raw=true)

Click the button `Test` and expect the output `Connected Successfully`

![image](images/studio_test_connection4.png?raw=true)

**✅ 5f. Open the notebook Work**

Use the ellipsis to now select `Notebooks`

![image](images/studio_home.png?raw=true)

Once the notebook opens it asks you to create the graph: click the `Create Graph` button (and leave all settings to default)

![image](images/studio_create_graph.png?raw=true)

Execute cell after cell spotting the `Real Time >` button in each cell (top right) 

![image](images/studio_notebook_1.png?raw=true)

Voila ! 

![image](images/studio_notebook_2.png?raw=true)

**✅ 5g. Close Notebook**

To close open notebooks you can now use

```bash
docker-compose down
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose down
Stopping workshop-introduction-to-nosql_studio_1 ... done
Stopping dse                                     ... 
```

Congratulations, you completed the Graph Database practice!

Back to [main README](README.md#practice).