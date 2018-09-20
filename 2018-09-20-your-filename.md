## Cisco Umbrella Introduction

- Cisco Umbrella is Cisco’s Secure Internet Gateway. 
- It provides the first line of defense against threats on the internet wherever users go. 
- By analyzing and learning from internet activity patterns, Umbrella automatically uncovers attacker infrastructure staged for current and emerging threats, and proactively blocks malicious requests before they reach a customer’s network or endpoints. 
- You can stop phishing and malware infections earlier, identify already infected devices faster, and prevent data exfiltration. 
- Because Umbrella is built into the foundation of the internet and delivered from the cloud, it provides complete visibility into internet activity across all locations and users. 
- Plus it’s one of the simplest security products to deploy and manage.
- Umbrella can be the first line of defense against threats by preventing devices from connecting to malicious or likely malicious sites in the first place—which significantly reduces the chance of malware getting to your network or endpoints.
- We use DNS as one of the main mechanisms to get traffic to our cloud platform, and then use it to enforce security too. 
- DNS is a foundational component of how the internet works and is used by every device in the network. 
- Way before a malware file is downloaded or before an IP connection over any port or any protocol is even established, there’s a DNS request.
 
## Advantage of Cisco Umbrella
#### Fastest and most reliable cloud infrastructure 
When customers connect to a cloud security platform, performance is critical. It cannot break or slow down internet connections. Since our network was established in 2006, we’ve had 100% uptime. And not just that, but we have peering relationships with over 500 ISPs which allow us to resolve requests faster – most customer actually report a boost in speed. 

#### Most open platform 
Leveraging our bi-directional API, customers can easily integrate Umbrella with existing tools to automatically add to our platform or enhance another system – extending protection and enhancing information. 

#### Most predictive intelligence 
Our unparalleled intelligence enables us to uncover malicious domains, IPs, and URLs before they’re even used in attacks. 

#### Easiest deployment 
There’s no hardware to install or software to manually update, and customers can leverage their existing Cisco footprint to provisions thousands of network devices and laptops. 

#### Broadest coverage of malicious destinations and files 
Now, not only do we have the power of the malicious domains and IPs that we had in Umbrella before, but we also have coverage for malicious files that are attempted to be downloaded from risky domains - and this is through the power of AMP. 

## How Cisco Umbrella Works
When Umbrella receives a DNS request, it first identifies which customer the request came from, and which policy to apply.
Next, Umbrella determines if the request is:  
 -  Safe or whitelisted, 
 -  Malicious or blacklisted, OR  
 -  “Risky”

For:
Safe requests, we route the connection as usual, and
 - Malicious requests, route the connection to a block page
 - For “risky” requests, route the connection to our cloud-based proxy for deeper inspection

### Whats "risky" requests
Most phishing, malware, ransomware, and other threats are hosted at domains that exhibit malicious behavior and are blocked at the domain level accordingly. But some domains host both malicious and safe content — this is an example of a domain that we would consider “risky”. These often allow users to upload and share content making them difficult to police. We need more info to accurately determine if these are safe or malicious. 

And, unlike traditional web proxies or gateways that examine all internet requests which adds latency and complexity, our proxy ONLY intercepts and inspects requests for risky domains. 

### What after inspection

Starting with URL inspection, we use Cisco Talos intelligence, the Cisco web reputation system, and other third-party feeds to determine if a URL is malicious. You can also create a list of custom URLs to be blocked based on their policies.

If the disposition is still unknown after the URL inspection, and the web address is for a web hosted file that matches our 150+ file types – for example .PDF, .JPG and many more - we look at file reputation. We use AV engines and Cisco Advanced Malware Protection (AMP) to block malicious files before downloaded.

**This is where things gets interesting. How about using Cisco Umbrella integrated IOS-XR on vBNG for DNS queries.Continue to read further.............**

### vBNG Introduction
The Cisco IOS XRv 9000 Router is a cloud-based router that is deployed on a virtual machine (VM) instance on x86 server hardware running 64-bit IOS XR software. Cisco IOS XRv 9000 Router provides traditional Provider Edge services in a virtualized form factor, as well as virtual Route Reflector capabilities. Cisco IOS XRv 9000 Router is based on Cisco IOS XR software, so it inherits and shares the wide breadth of routing functionality available on other IOS XR platforms.

When the Cisco IOS XRv 9000 Router virtual IOS XR software is deployed on a VM, the Cisco IOS XR software functions just as if it were deployed on a traditional Cisco IOS XR hardware platform. The Cisco IOS XRv 9000 Router combines Route Processor, Line Card, and virtualized forwarding capabilities into a single, centralized forwarding instance. The Cisco IOS XRv 9000 Router has a fully featured, high speed virtual x86 data plane.

### HOW vBNG (Virtual Broadband Network Gateway) COMES INTO PICTURE

As most of DNS queries are from Broadband customers , its obvious to interact have first hop security. 

Admin can create required list of policies in the Umbrella dashboard. Register and create multiple device-ids based upon the policy rules. Provision the device-id in VBNG subscriber templates (as Static config or via Radius).

![Screen Shot 2018-09-20 at 11.31.22 AM.png]({{site.baseurl}}/images/Screen Shot 2018-09-20 at 11.31.22 AM.png)

VBNG filters the DNS requests from subscribers, add the 30 bytes EDNS header as additional RR and forward it to the original destination. Only IP/UDP length & checksum fields are updated in the packet. Other fields are not modified. DNS reply from server is not considered, it is forwarded to client as it is without any additional processing.
If input DNS request already has any additional-RR, the Umbrella EDNS header is added in the end of the packet. VBNG will not validate if there is already an Umbrella EDNS header present in the DNS request.


## Prerequisites and Authentication
As an integration partner, Cisco Umbrella will provision an Umbrella account within an organization (org) that has access to Network Devices. We will also provide an API Key to be used by all of your devices (or if desired, one for each model of device).

You will need to provide Umbrella with a working email address to be used for the account, this email should be associated with your company and not be for personal use.

If you are not an integration partner but are interested in becoming one, please email **umbrella-support@cisco.com.**

The Umbrella Network Devices API utilizes a vendor-specific API Key and a customer-specific API Token for authentication. All requests to the API must contain these parameters in an HTTP header. A token can only be used for the organization it has been generated for.

HTTPS/SSL is required for all API queries, calls made over plain HTTP will fail. Responses will be in JSON-encoded data.

The device should allow for the Customer API Token to be designated via some method (user input, configuration file, central configuration utility, etc.)

You will obtain this API Token from the Network Devices page of their Umbrella dashboard: _**https://dashboard.umbrella.com/#configuration/identities/networkdevices**_

Tokens can be refreshed by the customer in their dashboard. When the token is refreshed, all prior tokens will immediately expire and can no longer be used for authentication.

The API Key and API Token should be sent in an "Authorization" HTTP header, in the following format:
	_**Authorization:OpenDNS,apikey="<APIKEY>",token="<CUSTOMERTOKEN>"**_

## Registration API Endpoint

Post the appropriate data (as below) to the Registration API Endpoint to register a device into the customer dashboard.

The Registration API Endpoint is:
_**https://api.opendns.com/v3/networkdevices**_

The response will contain a unique identifier (“Device ID”), which should be stored and used in EDNS queries by the device. This identifier is how the Cisco Umbrella's global network DNS resolvers will know how to apply the proper customer policy for this specific device.

If you attempt to register a device that already exists, the API will return the same 200/OK with response data as if it had registered the first time. This allows a device with the same model and MAC address to retain the same identity even if the stored device loses its Device ID.

The combination of model, MAC address, and (optional) tag must be unique within the organization. If you would like to register a particular device multiple times and receive multiple Device IDs to be used by a single device (perhaps one for corporate access and one for guest access), use a different tag field.


#### How to Request

_curl -X POST -H "Content-Type: application/json" -d '{"model":"CiscoIOSXRv9000","macAddress":"18F145989CBC","label":"Test Device 1","name":"Test Device 1","serialNumber":"12345a"}'"https://management.api.umbrella.com/v1/organizations/2506118/networkdevices" --user '<API Key>:<API Secret>'_
  
![Screen Shot 2018-09-19 at 11.39.06 AM.png]({{site.baseurl}}/images/Screen Shot 2018-09-19 at 11.39.06 AM.png)

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
