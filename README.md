# Workstation

VMware Workstation Pro REST API allow you to interact programmatically with hypervisor and VMs.

## Setup

First install VMware Workstation Pro 16. Once installed, configure credetials invoking ```vmrest.exe --config``` (Windows) located in main installation folder. The interactive wizard will ask you for Username and password which will required to autheticate to the service. These credentials are then stored in ```%USERPROFILE%\vmrest.cfg```

To enable HTTPS service, generate a self-signed certificate and private key.
```
openssl req -x509 -newkey rsa:4096 -keyout workstationapi-key.pem -out workstationapi-cert.pem -days 365 -nodes
```

Finally, start the API service.
```
vmrest.exe -c workstationapi-cert.pem -k workstationapi-cert.pem
VMware Workstation REST API
Copyright (C) 2018-2020 VMware Inc.
All Rights Reserved

vmrest 1.2.1 build-16894299
-
Using the VMware Workstation UI while API calls are in progress is not recommended and may yield unexpected results.
-
Serving HTTPS on 127.0.0.1:8697
-
Press Ctrl+C to stop.
```

The ```vmrest``` process needs to run in order to process API requests. By default the service runs as the user who started it. You might want to start this process after Windows starts, so it is available right away. 



## Testing

```
curl 'https://127.0.0.1:8697/api/vms' -k -X GET --header 'Accept: application/vnd.vmware.vmw.rest-v1+json' --header "Authorization: Basic `echo -n username:password | base64`"
[
  {
    "id": "B65LGVA9R4HBIJFVRQSSI3C3SF05LU24",
    "path": "C:\\Users\\maros\\.docker\\machine\\machines\\dev\\dev.vmx"
  },
  {
    "id": "8OLTM5IPNE68U7FP5D61AMA4KLHK84G3",
    "path": "C:\\Users\\maros\\Documents\\Virtual Machines\\w10\\w10.vmx"
  }
]
curl 'https://127.0.0.1:8697/api/vms/B65LGVA9R4HBIJFVRQSSI3C3SF05LU24' -k -s -X GET --header 'Accept: application/vnd.vmware.vmw.rest-v1+json' --header "Authorization: Basic `echo -n username:password | base64`" | jq '.cpu.processors'
1
```
