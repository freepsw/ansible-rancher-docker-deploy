Role Name
=========

A role used to register hosts to Rancher's server, by running rancher_agent Docker container and providing the agent with the name and the registration script.

Role Variables
--------------
**rancher_server:** The IP of Rancher platform.

**rancher_port:** The port of Rancher platform.

**rancher_agent_name:** The name of the agent container.

**rancher_agent_version:** The version of the agent.


Dependencies
------------
This role requires Docker to be installed on the server.

