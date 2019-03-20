# General guidelines
Details on how to use the below templates to configure an Juniper MX equipment are displayed in the [README.md](https://github.com/kentik/config-snippets/blob/master/README.md) file at the root of this repository

# Juniper MX configuration notes
## Miscellaneous useful notes
* by default, Juniper devices export flow data as JFLOW (~ NetFlow v5), but the MX series can be set to export in-line Jflow as IPFIX instead. 
  * If unable to support in-line JFlow, try using Netflow v5 instead
* routers must persist SNMP interface IDs across reboots, this is usually done by default on MX devices
* Juniper MX default flow table size may not be large enough for some networks/environments, which will result in under-reporting in the flow data

## Adjusting Flow-table size:
The Juniper MX default flow table size may not be large enough for some networks/environments, which will result in under-reporting in the flow data. The flow table size can be adjusted in increments of 256K flows with the following configuration:
```
chassis {
  # for MX-104, use "afeb slot 0"
  fpc 0 {
    inline-services {
      flow-table-size {
        # 15 × 256K = ˜4M
        ipv4-flow-table-size 15;
      }
    }
  }
}
```
## additional config required on linecards and interfaces
The ```ipfix-interfaces.conf``` and ```ipfix-linecards.conf``` display these additionaly required stanzas.
* Add this one to every active lincard:
```
chassis {
  fpc 0 {
    sampling-instance KENTIK;
  }
  fpc 1 {
    sampling-instance KENTIK;
  }
}
```
* And add this to active interfaces:
```
family inet {
  sampling {
    input;
  }
}
```
