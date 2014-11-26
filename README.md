# AZMQ Boost Asio + ZeroMQ

## Welcome
The azmq library provides Boost Asio style bindings for ZeroMQ

This library is built on top of ZeroMQ's standard C interface and is
intended to work well with C++ applications which use the Boost libraries
in general, and Asio in particular.

The main abstraction exposed by the library is azmq::socket which
provides an Asio style socket interface to the underlying zeromq socket
and interfaces with Asio's io_service().  The socket implementation
participates in the io_service's reactor for asynchronous IO and
may be freely mixed with other Asio socket types (raw TCP/UDP/Serial/etc.).

## Building and installation

Building requires a recent version of CMake (2.8 or later), and a C++ compiler
which supports '-std=c++11'.  Currently this has been tested with -
* OSX10.9 Mavericks XCode6
* Arch Linux, Ubuntu w/GCC4.8

Library dependencies are -
* Boost 1.53 or later
* ZeroMQ 4.0.x

To build -
```
$ mkdir build && cd build
$ cmake ..
$ make
$ make test
$ make install
```

To change the default install location use -DCMAKE_INSTALL_PREFIX when invoking cmake
You can also change where the build looks for Boost and CMake by setting -

```
$ export BOOST_ROOT=<my custom Boost install>
$ export ZMQ_ROOT=<my custom ZeroMQ install>
$ mkdir build && cd build
$ cmake ..
$ make
$ ...
```

## Example Code
This is an aziomq version of the code presented in the ZeroMQ guide at
http://zeromq.org/intro:read-the-manual

```
#include <azmq/socket.hpp>
#include <boost/asio.hpp>
#include <array>

namespace asio = boost::asio;

int main(int argc, char** argv) {
    asio::io_service ios;
    azmq::socket subscriber(ios, ZMQ_SUB);
    subscriber.connect("tcp://192.168.55.112:5556");
    subscriber.connect("tcp://192.168.55.201:7721");
    subscriber.set_option(aziomq::socket::subscribe("NASDAQ"));

    azmq::socket publisher(ios, ZMQ_PUB);
    publisher.bind("ipc://nasdaq-feed");

    std::array<char, 256> buf;
    for (;;) {
        auto size = subscriber.receive(asio::buffer(buf));
        publisher.send(asio::buffer(buf));
    }
    return 0;
}
```

Further examples may be found in doc/examples

## Copying

Use of this software is granted under the the BOOST 1.0 license
(same as Boost Asio).  For details see the file `LICENSE-BOOST_1_0
included with the distribution.

## Contributing

AZMQ uses the [C4.1 (Collective Code Construction Contract)](http://rfc.zeromq.org/spec:22) process for contributions.
See the accompanying CONTRIBUTING file for more information.
