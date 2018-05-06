Lua I2C Binding
===============

> *Author: Frank Edelhaeuser*

This is the Lua I2C binding. This binding provides access from Lua scripts to I2C slave devices on I2C busses supported by the Linux kernel.

*Note:* This binding will not compile or work on Windows because there is no OS support for I2C in Windows.


#### Warning ####

This program can confuse your I2C bus, cause data loss and worse!

(The above note was copied from the [i2cdetect](https://linux.die.net/man/8/i2cdetect) series of Linux tools. Don't worry. If you're careful, it won't.)


#### Compile and Install ####

Lua I2C is available on [LuaRocks](https://luarocks.org/modules/mrpace2/i2c):

    $ luarocks install i2c

You may also clone this repo and compile the Lua I2C extension library for your platform:

    $ git clone https://github.com/mrpace2/lua-i2c
    $ cd lua-i2c
    $ make

Depending on your setup, you may need additional prerequisite packages for sucessfully compiling this binding. `lua-i2c` requires GCC, I2C headers and 
Lua headers for your version of Lua. `lua-i2c` has been tested with Lua 5.1, 5.2 and 5.3.

If `make` fails, carefully inspect the `make` output for any error messages. You may specify additional compiler options with the `make` command:

    $ make CFLAGS=-I/usr/include/lua5.3

When `make` succeeds, it generates a shared library ``i2c.so``. Place this file into a location where Lua searches for extension modules. You may run:

    $ lua -l i2c

to display a list of directories where Lua searches for extension modules.


#### Code Example ####

    local i2c = require("i2c")

    -- read 32 bytes from I2C device at bus 0, address 0x50
    result, data = i2c.read(0, 0x50, 32)
    if (result ~= i2c.SUCCESS) then
        i2c.error(result, true)
    end

    -- write bytes 0x00, 0x01, 0x02 to I2C device at bus 0, address 0x50
    result = i2c.write(0, 0x50, '\0\1\2')
    if (result ~= i2c.SUCCESS) then
        i2c.error(result, true)
    end

    -- write byte 0x12 to I2C device at bus 0, address 0x50 followed by
    -- repeated start and read 8 bytes
    result, data = i2c.writeread(0, 0x50, '\18', 8)
    if (result ~= i2c.SUCCESS) then
        i2c.error(result, true)
    end

See the API Reference below for more details on the services provided by the Lua I2C binding.


#### API Reference ####


This reference assumes that the Lua I2C binding was instantiated using this statement:

    local i2c = require("i2c")

You may use Lua 5.3's `string.pack` and `string.unpack` functions for encoding and decoding byte-packed data strings 
(e.g. `'\0\1\2'` in the example above). If you're stuck with Lua 5.1 or 5.2 you may use the [struct](http://www.inf.puc-rio.br/~roberto/struct/) 
library or the [lpack](https://luarocks.org/modules/luarocks/lpack) extension.


##### Read from I2C slave #####

    result, read-data = i2c.read(bus, address [, read-length])

Performs a read transfer on the I2C bus consisting of these steps: 

  * START condition
  * Send read address
  * Receive read data (optional, if read-length > 0)
  * STOP condition

Parameters:

  * *bus*: I2C bus number (integer, required)
  * *address*: 8-bit I2C slave address (integer, required)
  * *read-length*: Positive number of bytes to read (integer, optional, default = 0)

Return Values:

  * *result*: i2c.SUCCESS on success, i2c.ERR_* on error (integer)
  * *read-data*: data read from I2C slave (string)


##### Write to I2C slave #####

    result = i2c.write(bus, address [, write-data])

Performs a write transfer on the I2C bus consisting of these steps: 

  * START condition
  * Send write address
  * Send write data (optional, if length of write-data > 0)
  * STOP condition

Parameters:

  * *bus*: I2C bus number (integer, required)
  * *address*: 8-bit I2C slave address (integer, required)
  * *write-data*: Write data (string, optional, default = "")

Return Values:

  * *result*: i2c.SUCCESS on success, i2c.ERR_* on error (integer)


##### Write to I2C slave followed by read #####

    result, read-data = i2c.writeread(bus, address [, write-data [, read-length]])

Performs a write-read transfer on the I2C bus consisting of these steps: 

  * START condition
  * Send write address
  * Send write data (optional, if length of write-data > 0)
  * REPEATED START condition
  * Send read address
  * Receive read data (optional, if read-length > 0)
  * STOP condition

Parameters:

  * *bus*: I2C bus number (integer, required)
  * *address*: 8-bit I2C slave address (integer, required)
  * *write-data*: Write data (string, optional, default = "")
  * *read-length*: Positive number of bytes to read (integer, optional, default = 0)

Return Values:

  * *result*: i2c.SUCCESS on success, i2c.ERR_* on error (integer)
  * *read-data*: data read from I2C slave (string)


##### Translate I2C result code into string, optionally raise error #####

    errmsg = i2c.error(result [, raise])

Translates I2C operation result into human readable string and raises a Lua error if *raise* evaluates to `true` and *result* indicates an error.

Parameters:

  * *result*: I2C result code (i2c.SUCCESS or i2c.ERR_*) (integer, required)
  * *raise*: raise Lua error (boolean, optional, default = false)

Return Values:

  * *errmsg*: human readable error message describing the result code (string)


#### Support and Related Information ####

Support for the Lua I2C Binding is provided using the Github issues system: https://github.com/mrpace2/lua-i2c/issues. Please DO NOT approach the author with support requests by email. Thank you!

  * http://www.lua.org/manual/
  * http://www.lm-sensors.org/wiki/i2cToolsDocumentation
