---
title: Integration
---



### Home Location Registry


### HlrCluster Class Overview

The **HlrCluster** class is designed to manage both **active** and **standby** Home Location Registers (HLRs) for mobile network operations.

- Ensures that the standby node is also provisioned with the same changes as the active node.
- Asynchronous operations using python rq and Redis avoids any potential delays in provisioning towards active node. It also allows for standby node downtime as queued jobs are retried.


``` py title="Partial snippet of class used for HLR active/standby provisioning"
import re
import telnetlib
from telnetlib import IAC, DO, DONT, WILL, WONT, SB, SE, TTYPE
import emalib
from collections import OrderedDict
import logging
from rq import Queue
from redis import Redis
from packages.hlr import Hlr

class HlrCluster:
    def __init__(self, cluster_params):
        self.active = Hlr(**cluster_params['active']['params'])
        self.standby = Hlr(**cluster_params['standby']['params'])
        self.rq = Queue(cluster_params['standby']['queue'], connection=Redis())

    def login(self):
        return self.active.login()

    def logout(self):
        return self.active.logout()

    def get_subscriber_data(self, subscriber, parameter_type='MSISDN'):
        return self.active.get_subscriber_data(subscriber, parameter_type)

    def get_subscribers(self):
        return self.active.get_subscribers()

    def parse_subscriber_data(self, content):
        return self.active.parse_subscriber_data(content)

    def change_subscriber_profile(self, msisdn, profile):
        result = self.active.change_subscriber_profile(msisdn, profile)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.change_subscriber_profile, msisdn, profile)
        return result

    def delete_subscriber(self, msisdn):
        result = self.active.delete_subscriber(msisdn)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.delete_subscriber, msisdn)
        return result

    def create_subscriber(self, msisdn, imsi, profile):
        result = self.active.create_subscriber(msisdn, imsi, profile)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.create_subscriber, msisdn, imsi, profile)
        return result

    def delete_imsi(self, imsi):
        result = self.active.delete_imsi(imsi)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.delete_imsi, imsi)
        return result

    def delete_master_subscriber(self, msisdn):
        result = self.active.delete_master_subscriber(msisdn)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.delete_master_subscriber, msisdn)
        return result

    def get_subscriber_pdp_contexts(self, msisdns):
        return self.active.get_subscriber_pdp_contexts(msisdns)

    def set_nam(self, msisdn, nam):
        result = self.active.set_nam(msisdn, nam)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_nam, msisdn, nam)
        return result

    def set_schar(self, msisdn, schar=4):
        result = self.active.set_schar(msisdn, schar)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_schar, msisdn, schar)
        return result

    def set_ssi(self, msisdn, ss):
        result = self.active.set_ssi(msisdn, ss)
        self.rq.enqueue(self.standby.set_ssi, msisdn, ss)
        return result

    def set_attribute(self, msisdn, attribute):
        result = self.active.set_attribute(msisdn, attribute)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_attribute, msisdn, attribute)
        return result

    def set_obp(self, msisdn, obp):
        result = self.active.set_obp(msisdn, obp)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_obp, msisdn, obp)
        return result

    def set_soclir(self, msisdn, soclir):
        result = self.active.set_soclir(msisdn, soclir)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_soclir, msisdn, soclir)
        return result

    def get_imeisv(self, imsi):
        return self.active.get_imeisv(imsi)

    def get_mobile_subscriber_state(self, imsi):
        return self.active.get_mobile_subscriber_state(imsi)

    def set_supplementary_service(self, subscriber, ss, status, fnum="", time=""):
        result = self.active.set_supplementary_service(subscriber, ss, status, fnum, time)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.set_supplementary_service, subscriber, ss, status, fnum, time)
        return result

    def enable_supplementary_service(self, subscriber, ss, fnum="", time=30):
        result = self.active.enable_supplementary_service(subscriber, ss, fnum, time)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.enable_supplementary_service, subscriber, ss, fnum, time)
        return result

    def disable_supplementary_service(self, subscriber, ss):
        result = self.active.disable_supplementary_service(subscriber, ss)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.disable_supplementary_service, subscriber, ss)
        return result

    def enable_forwarding_service(self, msisdn, fnum, ss, time=None):
        result = self.active.enable_forwarding_service(msisdn, fnum, ss, time)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.enable_forwarding_service, msisdn, fnum, ss, time)
        return result

    def disable_forwarding_service(self, msisdn, ss):
        result = self.active.disable_forwarding_service(msisdn, ss)
        if self.standby.provisioning is True:
            self.rq.enqueue(self.standby.disable_forwarding_service, msisdn, ss)
        return result

```