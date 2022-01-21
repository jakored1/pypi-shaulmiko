# This is the ShaulMiko v2 basic documentation (usage and examples).

### ShaulMiko main functionalities:


##### constructor:
	Opens an ssh session to target device.
	
	- host:
		IP or DNS name of the host we wish to connect to.
	- port:
		The port we wish to connect on via ssh.
	- username:
		Devices username.
	- password:
		Devices password.
	- set_terminal_length:
		In network devices the terminal length might limit the output returned by the device.
		To avoid this we run the command 'terminal length 0' by default on device connection.
		Set this parameter to False in order to avoid running the command.
	- get_pty:
		Creates the channel with a pty, true by default.
		Supports openning other session inside the ShaulMiko session.

##### close_session:
	Closes the ssh session.
	
##### execute:
	Sends a command to the ssh session and returns the output (will include device prompt at the end).
	
	- command:
		The command to send to the ssh session
	- read_until_string:
		By default the module will find the devices prompt,
		and will search for it after the command was entered to determine wether the command finished.
		read_until_string allows the user to manually define a string on which to stop reading the output.
	- timeout:
		By default there is no timeout and the module will continue to wait for output until device prompt (or read_until_string) is found.
		timeout allows user to stop reading output after X seconds.
	- clear_prompt:
		By default keeps the prompt at the end of the output, set to true to remove it
		note if you set this to true while or after executing a command that would change the prompt (sudo su, ssh)
		it will cause the code to hang, to execute such commands you should use the *write followed by *find_prompt,
		after that you may keep using execute
		
##### write:
	Sends a command to the ssh session.
	
	- command:
		The command to send to the ssh session.
	- no_newline:
		By default the 'write' function adds a newline ('\n') character at the end of the command,
		this flag stops this behaviour.
		
##### read_until:
	Reads the output of the ssh session until given string.
	
	- read_until_string:
		read_until_string allows the user to manually define the string on which to stop reading the output.
	- timeout:
		By default there is no timeout and the module will continue to wait for output until read_until_string is found.
		timeout allows user to stop reading output after X seconds.

##### read_until_timeout:
	Reads the output of the ssh session until X seconds passed.
	
	- timeout:
		After how many seconds to stop reading.

##### find_prompt:
	Finds the prompt of the device (Ex: 'MyRouter#' or '[root@mylinuxserver ~]#').
	
##### clear_buffer:
	Allows user to clear the ssh sessions output,
	cleaning it for the next command.
	(execute does this automatically unless read_until_string was given).



### Examples:
```python
Import ShaulMiko

session = ShaulMiko.ShaulMiko('1.1.1.1', 22, 'user', 'password')
output = session.execute('hostname')
session.close_session()
```

```python
Import ShaulMiko

session = ShaulMiko.ShaulMiko('1.1.1.1', 22, 'user', 'password')
session.write('enable')
output = session.read_until('Password:', timeout=5)
session.write('password')
output = session.read_until('#', timeout=5)
session.close_session()
```