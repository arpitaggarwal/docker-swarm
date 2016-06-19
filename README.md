

### How to get swarm?

Use the swarm image to execute a command.
```
docker run swarm --help
```
**Step 1:** Create and run a VM named swarm-manager using command: `docker-machine create -d virtualbox swarm-manager`

**Step 2:** Create and run a VM named swarm-node-1 using command: `docker-machine create -d virtualbox swarm-node-1`

**Step 3:** Create and run a VM named swarm-node-2 using command: `docker-machine create -d virtualbox swarm-node-2`

# Create a Swarm discovery token

Here we use the discovery backend hosted on Docker Hub to create a unique discovery token for your cluster. This discovery backend is only for low-volume development and testing purposes, not for production. Later on, when you run the Swarm manager and nodes, they register with the discovery backend as members of the cluster that’s associated with the unique token. The discovery backend maintains an up-to-date list of cluster members and shares that list with the Swarm manager. The Swarm manager uses this list to assign tasks to the nodes.


**Step 4:** Connect the Docker Client on your computer to the Docker Engine running on manager.

`eval $(docker-machine env swarm-manager)`

The client will send the docker commands in the following steps to the Docker Engine on swarm-manager.


**Step 5:** Create a unique id for the Swarm cluster:

`docker run --rm swarm create`

The docker run command gets the latest swarm image and runs it as a container. The create argument makes the Swarm container connect to the Docker Hub discovery service and get a unique Swarm ID, also known as a “discovery token”. The token appears in the output, it is not saved to a file on the host. The --rm option automatically cleans up the container and removes the file system when the container exits.

**Step 6:** List the VMs to check that they’re set up and running.

**Step 7:** Start swarm-manager

`docker run -d -p <your_selected_port>:3376 -t -v /var/lib/boot2docker:/certs:ro swarm manage -H 0.0.0.0:3376 --tlsverify --tlscacert=/certs/ca.pem --tlscert=/certs/server.pem --tlskey=/certs/server-key.pem token://<cluster_id>
`

**Step 8:**  Connect Docker Client to swarm-node-1.
`eval $(docker-machine env swarm-node-1)
`

**Step 9:** Join swarm-node-1 to swarm-manager
`docker run -d swarm join --addr=<node_ip>:<node_port> token://<cluster_id>
`

**Step 10:**  Connect Docker Client to swarm-node-2.
`eval $(docker-machine env swarm-node-1)
`

**Step 11:** Join swarm-node-2 to swarm-manager
`docker run -d swarm join --addr=<node_ip>:<node_port> token://<cluster_id>
`

**Step 12:** Make Docker swarm-manager as DOCKER_HOST
`DOCKER_HOST=<swarm-manager_ip>:<your_selected_port>
`

**Step 13:** Get information about the Swarm
`docker info
`

By default, Docker Swarm uses the “spread” strategy to choose which node runs a container. When you run multiple containers, the spread strategy assigns each container to the node with the fewest containers.


Reference: https://docs.docker.com/swarm/install-w-machine/
