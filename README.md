# Python API Client IBM Quantum Experience [![Build Status](https://travis-ci.org/QISKit/qiskit-api-py.svg?branch=master)](https://travis-ci.org/QISKit/qiskit-api-py)

The official API Client to use [IBM Quantum Experience](https://quantumexperience.ng.bluemix.net/) in Python.

This package can be used in [Jupyter Notebook](https://jupyter.org/).

* [Installation](#installation)
* [Getting Started](#getting-started)
* [Methods](#methods)
* [GIST Jupyter](#jupyter)
* [Reference](#reference)

## Installation

You can install me using `pip` or `easy_install`. For example, from the command line:

    $ pip install IBMQuantumExperience

To install the package in Jupyter, you have to run in a Notebook:

```python
import pip
def install(package):
   pip.main(['install', package])
install('IBMQuantumExperience')
```

or, if you want the standard output, one could even use the exclamation point:

```python
! pip install IBMQuantumExperience
```

### Getting Started

Now it's time to begin doing real work with Python and IBM Quantum Experience.

First, import our API Client:

```python
from IBMQuantumExperience import IBMQuantumExperience
```

Then, initialize your IBM Quantum Experience connection by supplying your *token*. You can obtain the token from *[My Account](https://quantumexperience.ng.bluemix.net/qx/account)* area of *Quantum Experience Platform* in *Personal Access Token* section.

```python
api = IBMQuantumExperience("543...9df")
```

The constructor takes also other optional arguments. The dictionary *config* allows with extra options to customize the connection (like the url of the API).
The boolean *verify* checks for SSL certificate errors:

```python
api = IBMQuantumExperience("543...9df", config = {"url":"https://..."}, verify=False)
```

By default, the config parameter is defined like:

```
config = {
   "url": 'https://quantumexperience.ng.bluemix.net/api'
}
```

But the config parameter can be store the *access_token* and the *user_id* to avoid login with API Token if you know this values.
Also all methods can be receives the *access_token* and the *user_id*. Also you can set the *client_application* to know what client is using the QX Platform. By Default the client is set to the this general api python.

If *verify* is set to `False`, ignore SSL certificate errors:

```
verify = True
```

### Methods

### User Info

To get the information about the credits of the user, you only need call to:

```python
api.get_my_credits()
```

#### Codes

To get the information of a Code, including the last executions about this Code, you only need the codeId:

```python
api.get_code("id_code")
```

To get the information about the last Codes, including the last executions about these Codes, you only need call:

```python
api.get_last_codes()
```

#### Execution

To get all information (including the Code information) about a specific Execution of a Code, you only need the executionId:

```python
api.get_execution("id_execution")
```

To get only the Result about a specific Execution of a Code, you only need the executionId:

```python
api.get_result_from_execution("id_execution")
```

#### Running [QASM 2.0](https://github.com/QISKit/qiskit-openqasm)

To execute a [QASM 2.0](https://github.com/QISKit/qiskit-openqasm) experiment:

```python
api.run_experiment(qasm, device, shots, name=None, timeout=60)
```

- **qasm**: The QASM 2.0 code to run. Eg: 
```qasm = 'OPENQASM 2.0;\n\ninclude "qelib1.inc";\nqreg q[5];\ncreg c[5];\nh q[0];\ncx q[0],q[2];\nmeasure q[0] -> c[0];\nmeasure q[2] -> c[1];\n'```
- **backend**: Type of backend to run the experiment. Options: *simulator*, or real backends (You can use the available_backends() method to get the availables backends), those are the real chips of 5 qubits. Eg:
```device = 'simulator' ```
- **shots**: Number of shots of the experiments. Maximum 8192 shots. Eg:
```shots = 1024 ```
- **name**: Name of the experiment. This paramater is optional, by default the name will be 'Experiment \#YmdHMS'. Eg:
```name = 'bell state experiment'``
- **timeout**: Time to wait for the result. The maximum timeout is 300. If the timeout is reached, you obtain the executionId to get the result with the getResultFromExecution method in the future. Eg:
```timeout = 120```

#### Running Jobs [QASM 2.0](https://github.com/QISKit/qiskit-openqasm)

To execute jobs about [QASM 2.0](https://github.com/QISKit/qiskit-openqasm) experiments:

```python
api.run_job(qasms, backend, shots, max_credits)
```

- **qasms**: A list of objects with the QASM 2.0 information. Eg: 
```
[
   { 'qasm': 'OPENQASM 2.0;\n\ninclude "qelib1.inc";\nqreg q[5];\ncreg c[5];\nh q[0];\ncx q[0],q[2];\nmeasure q[0] -> c[0];\nmeasure q[2] -> c[1];\n'},
   { 'qasm': 'OPENQASM 2.0;\n\ninclude "qelib1.inc";\nqreg q[5];\ncreg c[5];\nx q[0];\nmeasure q[0] -> c[0];\n'}
]
```
- **backend**: Type of backend to run the experiment. Options: *simulator*, or real backends (You can use the available_backends() method to get the availables backends). Eg:
```device = 'simulator' ```
- **shots**: Number of shots of the experiments. Maximum 8192 shots. Eg:
```shots = 1024 ```
- **max_credits**: Maximum number of the credits to spend in the executions. If the executions are more expensives, the job is aborted. Eg:
```max_credits = 3```

To get job information:

```python
api.get_job(id_job)
```

- **id_job**: The identifier of the Job. Eg: 
``` 
    id_job = '9de64f58316db3eb6db6da53bf9135ff'
```

To get all jobs information:

- **limit**: Number of jobs returned. Eg:
```limit=5 ```

```python
api.get_jobs(limit)
```

#### Get information about a Device

To know the status (if it is running or in maintenance) of a device (real chip 5Q by default) you can run:

```python
api.backend_status(backend)
```

- **backend**: The backend to get its availability. By default is the 5 Qubits Real Chip. Eg:
```backend='ibmqx4' ```

#### Get Calibration of a Backend

To know the last calibration of a backend (real chip 5Q by default) you can run:

```python
api.backend_calibration(backend)
```

- **backend**: The backend to get its last calibration. By default is the 5 Qubits Real Chip. Eg:
```device='ibmqx4' ```

#### Get Parameters Calibration of a Backend

To know the last parameters of calibration of a backend (real chip 5Q by default) you can run:

```python
api.backend_parameters(backend)
```

- **backend**: The backend to get its last calibration. By default is the 5 Qubits Real Chip. Eg:
```device='ibmqx4' ```

#### Get Available Devices

To know the devices where you can run (by name):

```python
api.available_backends()
```

#### Get IBM Q API Version

To know the version of the IBM Q API:

```python
api.api_version()
```

#### Get user @ IBM Q Platform
To know the information about an user in the IBMQ Platform

```python
api.get_user(email)
```
- **email**: The email of the user to get its information


#### Create an user @ IBM Q Platform
To add an user in the IBMQ Platform

```python
api.create_user(name, email, password, institution)
```
- **email**: The email of the user to add
- **name**: The name of the user to add
- **password**: The password of the user to add
- **institution**: The institution of the user to add


#### Edit an user @ IBM Q Platform
To edit an user in the IBMQ Platform

```python
api.edit_user(email, password, institution, blocked, credits, usernamepublic)
```
- **email**: The email of the user to edit
- **password**: The new password of the user
- **institution**: The new institution of the user
- **blocked**: Is the user blocked? (Boolean)
- **credits**: Credits to assign to the user
- **usernamepublic**: The public username for the user


#### Create user group @ IBM Q Platform
To create an user group in the IBMQ Platform

```python
api.create_user_group(name, description, is_general)
```
- **name**: The name of the group to create
- **description**: The description for the group
- **is_general**: Is this group general? (Boolean)


#### Edit an user group @ IBM Q Platform
To edit an user group in the IBMQ Platform


```python
api.edit_user_group(name, description, is_general)
```
- **name**: The name of the group to create
- **description**: The new description for the group
- **is_general**: Is this group general? (Boolean)


#### Get an user group @ IBM Q Platform by name
To get an user group in the IBMQ Platform by name

```python
api.get_user_group(name)
```
- **name**: The name of the group to get


#### Get all user groups @ IBM Q Platform
To get all user groups in the IBMQ Platform

```python
api.get_user_groups()
```


#### Set an user to an user group @ IBM Q Platform 
To set an user to a group of users in the IBMQ Platform

```python
api.set_user_group(email, name_user_group)
```
- **email**: The email of the user to add to the group
- **name_user_group**: The name of the group


#### Unset an user from an user group @ IBM Q Platform
To unset an user to a group of users in the IBMQ Platform

```python
api.unset_user_group(email, name_user_group)
```
- **email**: The email of the user to remove from the group
- **name_user_group**: The name of the group


#### Get all topologies available @ IBM Q Platform
To get all the topologies available in IBMQ Platform

```python
api.get_topologies()
```


#### Get a topology @ IBM Q Platform by name
To get a topology by name in IBMQ Platform

```python
api.get_topology(name)
```
- **name**: The name of the topology to get


#### Create a topology @ IBM Q Platform
To create a topology in IBMQ Platform

```python
api.create_topology(name, description, adjacency_matrix, qubits, execution_types, is_simulator, is_hidden, tasks_queue, results_queue, device_status_queue, status_queue, is_default, qasm_header, picture_url)
```
- **name**: The name of the topology to create
- **description**: The description for the topology
- **adjacency_matrix**: The adjacency matrix for the topology. Example: "0,1,1,0"
- **qubits**: The qubits for the topology.
- **execution_types**: The execution types for the topology. Example: "simulator,real"
- **is_simulator**: Is this topology created to be a simulator? (Boolean)
- **is_hidden**: Is this topology hidden? (Boolean)
- **tasks_queue**: The tasks queue of the topology
- **results_queue**: The results queue of the topology
- **device_status_queue**: The device status queue
- **status_queue**: The status queue of the topology
- **is_default**: Is default this topology? (Boolean)
- **qasm_header**: Specify the qasm header for this topology
- **picture_url**: Picture URL for the topology
    
    
#### Edit a topology @ IBM Q Platform by name
To edit a topology in IBMQ Platform

```python
api.edit_topology(name, description, adjacency_matrix, qubits, execution_types, is_simulator, is_hidden, tasks_queue, results_queue, device_status_queue, status_queue, is_default, qasm_header, picture_url)
```
- **name**: The name of the topology to be edited
- **description**: The description for the topology
- **adjacency_matrix**: The adjacency matrix for the topology. Example: "0,1,1,0"
- **qubits**: The qubits of the topology
- **execution_types**: The execution types for the topology. Example: "simulator,real"
- **is_simulator**: Is this topology created to be a simulator?  (Boolean)
- **is_hidden**: Is this topology hidden?  (Boolean)
- **tasks_queue**: The tasks queue of the topology
- **results_queue**: The results queue of the topology
- **device_status_queue**: The device status queue
- **status_queue**: The status queue of the topology
- **is_default**: Is default this topology?  (Boolean)
- **qasm_header**: Specify the qasm header for this topology
- **picture_url**: Picture URL for the topology


#### Get relations between an user group and a topology @ IBM Q
To get the relations between an user group to a topology

```python
api.get_user_groups_to_topology(name_topology, name_user_group)
```
- **name_topology**: The name of the topology
- **name_user_group**: The group of users related to topology


#### Set an user group to a topology @ IBM Q
To set an user group to a topology

```python
api.set_user_group_to_topology(name_topology, name_user_group, can_always_run)
```
- **name_topology**: The name of the topology
- **name_user_group**: The group of users related to topology
- **can_always_run**: Can always run? (Boolean)


#### Unset an user group from a topology @ IBM Q
To unset an user group from a topology

```python
api.set_user_group_to_topology(name_topology, name_user_group)
```
- **name_topology**: The name of the topology
- **name_user_group**: The group of users related to topology


#### Get backend by name @ IBM Q
To get backend by name.

```python
api.get_backend_by_name(name)
```
- **name**: The name of the backend to get


#### Get all backends available @ IBM Q
To get all backends

```python
api.get_backends()
```


#### Create a backend @ IBM Q
To create a backend

```python
api.create_backend(name, serial_number, type_device, version, chip_name, url_details, name_topology, status, description, gate_set, date_online)
```
- **name**: Name of the backend to be created
- **serial_number**: Serial number for the backend
- **type_device**: Backend's device type
- **version**: Backend's version
- **chip_name**: Chip name
- **url_details**: URL Details for the backend
- **name_topology**: Topology name to assign
- **status**: Backend's status
- **description**: Backend's description
- **gate_set**: Backend's gate set
- **date_online**: Date online


#### Edit a backend @ IBM Q
To edit a backend

```python
api.edit_backend(name, serial_number, type_device, version, chip_name, url_details, name_topology, status, description, gate_set, date_online)
```
- **name**: Name of the backend to be edited
- **serial_number**: Serial number for the backend
- **type_device**: Backend's device type
- **version**: Backend's version
- **chip_name**: Chip name
- **url_details**: URL Details for the backend
- **name_topology**: Topology name to assign
- **status**: Backend's status
- **description**: Backend's description
- **gate_set**: Backend's gate set
- **date_online**: Date online


#### Get all hubs available @ IBM Q
To get all hubs

```python
api.get_hubs()
```


#### Get a hub by name @ IBM Q
To get a hub by name

```python
api.get_hub(hub)
```
- **hub**: The name of the hub to get


#### Create a hub @ IBM Q
To create a hub in the system

```python
api.create_hub(hub, title, description)
```
- **hub**: The name of the hub to be created
- **title**: The title of the hub to be created
- **description**: The description of the hub to be created


#### Edit a hub @ IBM Q
To edit a hub by name

```python
api.edit_hub(hub, title, description)
```
- **hub**: The name of the hub to be edited
- **title**: The new title for the hub
- **description**: The new description for the hub


#### Remove a hub @ IBM Q
To remove a hub by name

```python
api.remove_hub(hub)
```
- **hub**: The name of the hub to be removed


#### Get all groups in a hub @ IBM Q
To get all groups in a hub by using hub's name

```python
api.get_groups(hub)
```
- **hub**: The name of the hub to be used


#### Get a group within a hub @ IBM Q
To get a group within a hub by using hub's and group's name

```python
api.get_group(hub, group)
```
- **hub**: The name of the hub to be used
- **group**: The name of the group to get within the hub


#### Create a group within a hub @ IBM Q
To create a group within a hub

```python
api.create_group(hub, group, title, description)
```
- **hub**: The name of the hub where the group will be created
- **group**: The name of the group to be created
- **title**: The title of the group to be created
- **description**: The description of the group to be created


#### Edit a group within a hub @ IBM Q
To edit a group within a hub

```python
api.edit_group(hub, group, title, description)
```
- **hub**: The name of the hub where the group will be edited
- **group**: The name of the group to be edited
- **title**: The new title for the group
- **description**: The new description for the group


#### Remove a group from hub @ IBM Q
To remove a group from hub by name

```python
api.remove_group(hub, group)
```
- **hub**:  The name of the hub where the device will be removed
- **group**: The name of the group to be removed


#### Get all projects in group/hub @ IBM Q
To get all projects in a group within a hub

```python
api.get_projects(hub, group)
```
- **hub**: The name of the hub to be used
- **group**: The name of the group to be used


#### Get a project within a group/hub @ IBM Q
To get a project by name in a group within a hub

```python
api.get_project(hub, group, project)
```
- **hub**: The name of the hub to be used
- **group**: The name of the group to be used
- **project**: The name of the project to get


#### Create a project in a group/hub @ IBM Q
To create a project related to group within a hub

```python
api.create_project(hub, group, project, title, description)
```
- **hub**: The name of the hub that hosts the group
- **group**: The name of the group where the project will be created
- **project**: The name of the project to be created
- **title**: The title of the project to be created
- **description**: The description of the project to be created


#### Edit a project in a group/hub @ IBM Q
To edit a project related to group within a hub

```python
api.edit_project(hub, group, project, title, description)
```
- **hub**: The name of the hub that hosts the group
- **group**: The name of the group where the project will be edited
- **project**: The name of the project to be edited
- **title**: The new title for the project
- **description**: The new description for the project


#### Remove a project in a group/hub @ IBM Q
To remove a project from a group within a hub

```python
api.remove_project(hub, group, project)
```
- **hub**: The name of the hub where the group is registered
- **group**: The name of the group where the project will be removed
- **project**: The name of the project to be removed


#### Get all users in a hub @ IBM Q
To get all users in a hub (and its groups/projects)

```python
api.get_users(hub)
```
- **hub**: The name of the hub where the user is registered


#### Add an user to a hub, group or project @ IBM Q
To add an user to a hub, group or project

```python
api.add_user(email, hub, group, project, role)
```
- **hub**: The name of the hub where the user will be registered
- **group**: The name of the group where the user will be registered (optional)
- **project**: The name of the project where the user will be registered (optional)
- **email**: The email of the user to be added
- **role**: The role that will have the user within the hub (only required in the case of a hub)


#### Remove an user from a hub, group or project @ IBM Q
To remove an user from a hub, group or project

```python
api.remove_user(email, hub, group, project)
```
- **email**: The email of the user to be removed
- **hub**: The name of the hub where the user will be removed
- **group**: The name of the project where the user will be removed
- **project**: The name of the project where the user will be removed


#### Get all devices in a hub @ IBM Q
To get all devices in a hub (and its groups/projects)

```python
api.get_devices(hub)
``` 
- **hub**: The name of the hub where the devices are registered


#### Add a device to a hub, group or project @ IBM Q
To add a device to a hub, group or project

```python
api.add_device(device, priority, hub, group, project)
``` 
- **device**: The name of the device to be added
- **priority**: The priority of the device to be added
- **hub**: The name of the hub where the device will be added
- **group**: The name of the group where the device will be added (optional)
- **project**: The name of the project where the device will be added (optional)


#### Remove device from a hub, group or project @ IBM Q
To remove device from a hub, group or project

```python
api.remove_device(device, hub, group, project)
``` 
- **device**: The name of the device to be removed
- **hub**: The name of the hub where the device will be removed
- **group**: The name of the group where the device will be removed (optional)
- **project**: The name of the project where the device will be removed (optional)


## Deploy and Test

If you want participate in the project, you can clone the repository and install the dependencies to run it.

You can do a pull request to improve or add any functionality.

You can run the tests under ```test``` folder. See the test/README file to more information.

## Reference

[IBM Quantum Experience Tutorial](https://quantumexperience.ng.bluemix.net/qstage/#/tutorial?sectionId=c59b3710b928891a1420190148a72cce&pageIndex=0)

[IBM Quantun Experience Community](https://quantumexperience.ng.bluemix.net/qstage/#/community)

[OPENQASM](https://github.com/QISKit/qiskit-openqasm)
