# ChatterTools

## What is ChatterTools?
ChatterTools is a Python library built by [Chatter](https://chatter.dev) to help developers work with CNC machines. In our initial release, ChatterTools includes a library for interacting with Fanuc machines via the FOCAS protocol.

## Fanuc FOCAS Wrapper
FOCAS is a protocol made by Fanuc for interacting with Fanuc controls. Traditionally, this is programmed in C++ or C#. ChatterTools provides a simplified Python wrapper on top of the Fanuc drivers compatible with both Windows and Linux.

## Installation
To install ChatterTools, run the following command:
```bash
pip install chattertools
```

## Usage
To use ChatterTools, first import the library:
```python
import chattertools as ch
```

Next, create a new Fanuc object:
```python
focas = ch.Focas(ip='10.0.2.124')
```

Test a query to the Fanuc machine:
```python
print(focas.cnc_exeprgname2())
```

Close the connection and free resources:
```python
focas = None
```

## Approach
Our goal with the Focas wrapper is to build a simple Python wrapper that removes the complexities in the base library (like passing through handles and pointers) and instead provides a more Pythonic interface. In doing this, we want to make sure that the original FOCAS documentation and function names are still preserved, so that you can reference the original Fanuc documentation for function information, both in usage and contributing to this project.

## Example Function Comparison
In a traditional implementation as specified by Fanuc, it is required that you pass in a handle and a pointer to a struct. This causes a headache when all you really want to do is just get something simple, like the program name. Here is an example of how you would get the program name in a traditional implementation:

```c++
protected override string GetActiveProgramName()
{
	short ret;

	// Check we have a valid handle
	if (HSSBHandle == 0)
		return "Unavailable";

	// Create an instance of our structure
	Focas1.ODBEXEPRG prgm = new Focas1.ODBEXEPRG();

	// Ask Fanuc for the status information
	ret = Focas1.cnc_exeprgname(HSSBHandle, prgm);

	// Check to make sure the call was successfull
	// and convert the mode to a string and return it.
	if (ret == Focas1.EW_OK)
		return new string(prgm.name.Where(c => !char.IsControl(c)).ToArray());

	LogError(ret);
	return "Unavailable";
}
```

In our implementation, we have simplified this to the following:

```python
def getActiveProgramName(self):
    # Read the program name
    return self.focas.cnc_exeprgname2().name
```

## Currently Implemented Functions
There is a whole list of FOCAS functions available from (Inventcom)[https://www.inventcom.net/fanuc-focas-library/]. The following functions are currently implemented

- cnc_allclibhndl3 - Get a connection handle
- cnc_freelibhndl - Free a connection handle
- cnc_exeprgname - Get the active program name and O number
- cnc_exeprgname2 - Get the active program full path
- cnc_acts - Get the actual spindle speed of the machine
- cnc_statinfo - Get the status of the machine (mode, alarm, e-stop, and more)
- cnc_rdalmmsg - Get any active alarm messages
- cnc_rdopmsg3 - Get any active operator messages
- cnc_rdmacro - Read a macro variable
- cnc_wrmacro - Write a macro variable

## Contributing
We welcome contributions to this library! We have only scratched the surface of the FOCAS. The Inventcom website has a full list of functions available, and all contributions should follow the same pattern. When adding a new function, please start with the same exact function name and arguments as the original FOCAS library. Omit arguments that are unneeded, such as handles and pointers, and instead return the data directly. This will make it easier for users to reference the original documentation and for us to add more functions in the future. When returning data which comes in the form of an enum, please implement a mapping as shown inside the `obst` class in focas_structs.py/

## Future Plans
As of 3/29, this project is in very early stages. We will be adding a more detailed contribution guide as well as more functions in the coming days.
Long term, we will be adding compatibility with more types of CNC machines, as well as functions used to communicate directly with Chatter itself. Stay tuned!

## License
This project is licensed under the Apache 2.0 License - see the LICENSE file for details.

## Acknowledgements
- [Chatter.dev](https://chatter.dev)
- Pete Oxenham | X: [@peteoxenham](https://x.com/peteoxenham)
- Dave Precise | X: [@DDaveprecise](https://x.com/DDaveprecise)
- Ian Rist | X: [@suckitpete](https://x.com/suckitpete)