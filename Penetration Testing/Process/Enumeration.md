## Infrastructure Enumeration

We try to overview the company's position on the internet and intranet. For this, we use OSINT and the first active scans. We use services such as DNS to create a map of the client's servers and hosts and develop an understanding of how their `infrastructure` is structured. This includes name servers, mail servers, web servers, cloud instances, and more. We make an accurate list of hosts and their IP addresses and compare them to our scope to see if they are included and listed.

In this phase, we also try to determine the company's security measures. The more precise this information is, the easier it will be to disguise our attacks (`Evasive Testing`). But identifying firewalls, such as web application firewalls, also gives us an excellent understanding of what techniques could trigger an alarm for our customer and what methods can be used to avoid that alarm.

## Service Enumeration

In service enumeration, we identify services that allow us to interact with the host or server over the network (or locally, from an internal perspective). Therefore, it is crucial to find out about the service, what `version` it is, what `information` it provides us, and the `reason` it can be used. Once we understand the background of what this service has been provisioned for, some logical conclusions can be drawn to provide us with several options.

Many services have a version history that allows us to identify whether the installed version on the host or server is actually up to date or not. This will also help us find security vulnerabilities that remain with older versions in most cases. Many administrators are afraid to change applications that work, as it could harm the entire infrastructure. Therefore, administrators often prefer to accept the risk of leaving one or more vulnerabilities open and maintaining the functionality instead of closing the security gaps.

## Host Enumeration

We try to identify which `operating system` is running on the host or server, which `services` it uses, which `versions` of the services, and much more. Again, apart from the active scans, we can also use various OSINT methods to tell us how this host or server may be configured.

We can find many different services, such as an FTP server that the company uses to exchange data between employees and even allows anonymous access. Even today, there are many hosts and servers that the manufacturers no longer support. However, vulnerabilities are still found for these older versions of operating systems and services, which then remain and endanger our client's entire infrastructure.

It does not matter here whether we examine each host or server externally or internally. However, from the internal perspective, we will find services that are often not accessible from the outside. Therefore, many administrators become careless and often consider these services "secure" because they are not directly accessible from the internet. Thus, many misconfigurations are often discovered here due to these assumptions or lax practices. During host enumeration, we try to determine what role this host or server plays and what network components it communicates with. In addition, we must also identify which `services` it uses for this purpose and on which `ports` they are located.

During internal host enumeration, which in most cases comes after the successful `Exploitation` of one or more vulnerabilities, we also examine the host or server from the inside. This means we look for sensitive `files`, local `services`, `scripts`, `applications`, `information`, and other things that could be stored on the host. This is also an essential part of the `Post-Exploitation` phase, where we try to exploit and elevate privileges.

## Pillaging

Another essential step is `Pillaging`. After hitting the `Post-Exploitation` stage, pillaging is performed to collect sensitive information locally on the already exploited host, such as employee names, customer data, and much more. However, this information gathering only occurs after exploiting the target host and gaining access to it.

The information we can obtain on the exploited hosts can be divided into many different categories and varies greatly. This depends on the purpose of the host and its positioning in the corporate network. The administrators taking the security measures for these hosts also play a significant role. Nevertheless, such information can show the `impact` of a potential attack on our client and be used for further steps to `escalate our privileges` or `move laterally` further in the network.

#### Certificates

SSL/TLS certificates are another potentially valuable source of information if HTTPS is in use.