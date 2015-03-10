OpenStack Nova README
=====================
This is modified for the integration purpose of NDP media corp PaaS management system.

Install procedure
=================
.. code-block::

  apt-get install python-pip  python-dev libxml2-dev libxslt1-dev

  pip install oslo.messaging oslo.config oslo.db oslo.i18n oslo.utils oslo.serialization oslo.concurrency oslo.middleware oslo.log oslo.context psutil jinja2 lxml rfc3986 paste routes PasteDeploy python-glanceclient pyasn1 python-cinderclient
  easy_install -U Routes (resolve routes.meddleware dependence)

  apt-get install python-keystoneclient
  get clone https://github.com/openstack/python-keystoneclient.git

  export PYTHONPATH=/home/paasuser1/source/nova/:/home/paasuser1/source/python-keystoneclient/

  comments: nova/config.py (55: replace version with a const string)

  jay@paas1:~$ cat /usr/bin/nova-api 
  #!/usr/bin/python
  # PBR Generated from u'console_scripts'

  import sys
  from nova.cmd.api import main

  if __name__ == "__main__":
  sys.exit(main())


  paasuser1@paas-ubuntu-1:~/source/nova$ cat /etc/nova/nova.conf 
  [DEFAULT]
  #rabbit_host = localhost
  #rabbit_hosts = $rabbit_host:$rabbit_port
  #rabbit_port = 5672
  #rabbit_password = guest
  #rpc_backend = nova.openstack.common.rpc.impl_kombu
  enabled_apis = k8s
  k8s_listen_port = 8018

  paasuser1@paas-ubuntu-1:~/source/nova$ cat /etc/nova/api-paste.ini 
  [composite:k8s]                                                       
  use = call:nova.api.openstack.urlmap:urlmap_factory                             
 /k8s: k8shelper                                                 
  #/v2: openstack_compute_api_v2 


  [composite:openstack_compute_api_v2]                                            
  use = call:nova.api.auth:pipeline_factory             
  keystone = osapi_compute_app_v2


  [app:osapi_compute_app_v2]
  paste.app_factory = nova.api.openstack.compute:APIRouter.factory


  [pipeline:k8shelper]
  pipeline = k8shelperapp
  [app:k8shelperapp]
  paste.app_factory = nova.api.openstack.k8s:APIRouter.factory
