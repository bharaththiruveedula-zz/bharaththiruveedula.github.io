---
layout: post
author: Bharath Thiruveedula
title: Create VNFs on the fly using VNF Components in Tacker
date: 2016-08-02 23:23:06 +0530
categories:
- NFV
- OpenStack
tags:
- NFV
- OpenStack
---

Most of the Telcos are busy now building their own VNFs and searching for the most suitable Virtual Infrastructure Manager for running their VNFs(mostly solved :) ) As in this area it mostly involves proprietary software, we find very dry and fuzzy discussions on the web. So in this post I will try to discuss with examples. In this post we will learn about NFV nomenclature and discuss specifically about VNF Component by taking [project clearwater](http://www.projectclearwater.org/) as an example. It is an open source solution for IMS Core developed by MetaSwitch Networks.

**Architecture of vIMS:** ![Clearwater_Architecture](https://tbharath91.files.wordpress.com/2016/08/clearwater_architecture.png?w=300) Let's very briefly discuss about these components before we jump into NFV world.
**Bono**: Bono acts like SIP edge proxy for the rest of IMS system. And it also provides WebRTC interface for the clients. A client is anchored to a particular Bono node for the duration of its registration.
**Sprout**: Sprout is both IMS registrar and authoritative router proxy. So it mostly handles client authentication. **Homestead**: Homestead provides web service interface to sprout for retrieving the authentication credentials and user profile information.
**Homer**: Homer is a XML Document Management Server(XDMS) used to store MMTEL service settings document for each user. **Ralf**: Ralf main duty is to provide HTTP endpoint for storing bill related events.
**Ellis**: Ellis is a sample provisioning portal providing self sign-up, password management, line management and control of MMTEL service settings.

Assume we are building our own vIMS using the above VNFs. We have built these Virtual Network Functions, now the next step is to host these VNFs on top of any cloud infrastructure. This infrastructure which host any VNF is called Virtual Infrastructure Manager(VIM), for example OpenStack. But we need a service which can be part of the VIM that orchestrates and manages these VNFs, such service is called NFV Orchestrator and VNF Manager respectively. The most popular examples for NFV Orchestrator and VNF manager are [OpenStack Tacker](https://github.com/openstack/tacker), [OpenBaton](http://openbaton.github.io).

For explaining about VNF Components, we mainly concentrate on Tacker. Tacker is an OpenStack based both NFV Orchestrator and generic VNF Manager. But one of drawback or pain point of Tacker is to provide the Images beforehand for each of these components we discussed. The problem of this approach is whenever we want to launch the new version of your IMS, we have to build new Image and upload to cloud. So it is very hard to upgrade. So the solution for this problem is to use VNF components. How VNF components will solve this issue? Most of the NFV orchestrator consumes TOSCA templates both (NSD and VNFD) to launch the Network Service. In VNFD we have a new attribute called VNFC. Using this attribute we can specify shell script, ansible playbook or run puppet agent to install these network function. Using this approach we can easily upgrade the software in future and we can create the VNFs on fly. I am trying to show how we can create this vIMS solution and make a call on the fly using VNFC and Tacker in this OpenStack summit. You can be part of this demo by voting to this talk. How can you do that? Visit https://www.openstack.org/summit/barcelona-2016/vote-for-speakers/presentation/ and search for "CREATE VNFS ON THE FLY - VNF COMPONENTS IN TACKER" and cast your vote :) P.S: Though it is hypothetical scenario, I am running puppet server and IMS on different machines in my OpenStack setup at my home. And using port forwarding I have exposed those services. Initially all of my family members were registered to that service and communicating through SIP client(crazy adapters :P)
