#! /usr/bin/env python

import argparse
from collections import defaultdict as DefaultDict
import shlex
import subprocess

"""Displays information about how network interfaces connect.

This script displays connection-method information about the unique
network interfaces it detects.  It displays output in the form:

: interface-name,(wired|wireless),MAC

Copyright (C) 2014  Nick Daly

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or (at
your option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

"""

def execute(command):
    """Return a command's output.  Don't escape the command.

    Return empty strings instead of *None* if the specified command
    had no output or if it errored.

    """
    try:
        process = subprocess.Popen(
            shlex.split(command),
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE)
    except OSError:
        details = (None, None)
    else:
        details = process.communicate()

    return [(x if x else "") for x in details]

def parse_interface_and_macs():
    """Associate all interfaces with their MAC addresses.

    Parse *ifconfig* output and record each interface's MAC address.
    Also, record the interfaces that share MAC addresses.

    """
    output, error = execute("ifconfig -a")
    interfaces, macs = dict(), DefaultDict(list)

    for line in output.splitlines():
        if not line.split() or line.startswith(" "):
            continue
        line = line.split()

        interface = line[0]
        mac = line[-1]

        interfaces[interface] = mac
        macs[mac] += interface

    return interfaces, macs

def parse_connection_type(interfaces, macs):
    """Identify and record which interfaces are wired and wireless.

    *iwconfig* returns wireless interfaces in *stdout* and wired
    interfaces in *stderr*.  It's quite strange.

    """
    output, error = execute("iwconfig")
    wired, wireless = dict(), dict()

    wired = parse_iwconfig(wired, macs, error)
    wireless = parse_iwconfig(wireless, macs, output)

    return wired, wireless

def parse_iwconfig(interfaces, macs, details):
    """Actually parse the *iwconfig* output.

    Each *iwconfig* line that identifies an interface starts with the
    interface's name and contains data about the networks supported or
    the line ~no wireless extensions.~, if the interface is a wired
    interface.

    *iwconfig* doesn't currently appear to display interface aliases,
    so we can use its output to filter out the aliases that don't
    refer to real, physical interfaces.

    """
    for line in details.splitlines():
        if not line.split() or line.startswith(" "):
            continue
        line = line.split()


        interface = line[0]
        interfaces[interface] = macs[interface]

    return interfaces

if __name__ == "__main__":
    interfaces, macs = parse_interface_and_macs()
    wired, wireless = parse_connection_type(interfaces, macs)

    display_items = lambda items, type_: [
        ",".join([iface, type_, interfaces[iface]]) for iface in items]

    print("\n".join(display_items(wired, "wired")))
    print("\n".join(display_items(wireless, "wireless")))
