## rtipc

A Python binding for https://gitlab.com/etherlab.org/rtipc

Note: importing rtipc raises ImportError if `librtipc.so` is not available. Users should handle the exception.

## Example

```py
import numpy
import ctypes
try:
    import rtipc
except ImportError:
    rtipc = None

rtipcObj = rtipc.RtIPC('simulator')
rtipcGroup = rtipcObj.CreateGroup(1.0)
receiveMemory = numpy.zeros(16, numpy.uint8)
sendMemory = numpy.zeros(16, numpy.uint8)
connected = ctypes.c_uint8(0)
rtipcGroup.AddReceivePdo(b'receive', rtipc.DataType.uint8_T, receiveMemory.ctypes.data_as(ctypes.POINTER(ctypes.c_uint8)), 16, ctypes.byref(connected))
rtipcGroup.AddSendPdo(b'send', rtipc.DataType.uint8_T, sendMemory.ctypes.data_as(ctypes.POINTER(ctypes.c_uint8)), 16)

with rtipcObj:
    rtipcGroup.Receive()
    rtipcGroup.Send()
```

## Retaining Memory

While rtipc.RtIPC is running, please retain `receiveMemory`, `sendMemory` and `connected`; otherwise rtipcGroup.Receive/Send will cause SEGV. Especially be careful about `connected`.

If you are an experimented C++ programmer, you can imagine like AddReceivePdo/AddSendPdo is passing `shared_ptr<char[]>::get()` (and without retaining variables the shared_ptr is gone).

## License Note (also for contributors)

The license is the same as etherlab rtipc.

That means, if they (somehow) move to more permissive license, this binding license will be changed accordingly.

On the otherhand, if they unfortunately move to GPL, I will stop supporting this binding.

(Note sure if licensing in LGPL/MIT dual is safer though...)
