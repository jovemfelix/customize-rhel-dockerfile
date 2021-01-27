# Need to customize RHEL base Image?

Resolving the error:

`This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.`

## Source Folder Structure

```shell
.
├── base
│   └── Dockerfile	# Base Image with Subscription enabled
└── custom
    └── Dockerfile	# Custom imagem using yum install and using "base" as parent
```



# Steps using user credencials

## Base Imagem

> In this example we're customizing DotNet image 
>
> For making the registration you can pass the username and password

```dockerfile
FROM registry.redhat.io/dotnet/dotnet-31-rhel7

ARG  USERNAME
ARG  PASSWORD

USER 0

RUN subscription-manager register --username ${USERNAME} --password ${PASSWORD} --auto-attach && \
	yum update -y && \
	yum repolist
```

### Build base Image

> Here we're building de Dockerfile that is into "base" folder

```shell
$ docker build -t base-dotnet-31-rhel7 --build-arg USERNAME=your.username --build-arg PASSWORD=your.password --no-cache custom
```

## Custom Imagem

> In this example we're customizing DotNet image 
>
> For making the registration you can pass the username and password

```dockerfile
FROM base-dotnet-31-rhel7

RUN yum install -y libpng libjpeg libX11 libXext libXrender xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi
RUN yum downgrade -y openssl-libs-1.0.2k-16.el7
RUN yum install -y openssl-1.0.2k-16.el7
```



### Build custom Image

> Here we're building de Dockerfile that is into "custom" folder

```shell
$ docker build -t custom-dotnet-31-rhel7 --no-cache custom
```



> This example will be used as Builder image !



# Steps for Satellite credencials

## Base Imagem

> In this example we're customizing DotNet image 
>

```dockerfile
FROM registry.redhat.io/dotnet/dotnet-31-rhel7
USER 0
RUN unset no_proxy && \
    unset https_proxy && \
    unset NO_PROXY && \
    unset HTTPS_PROXY && \
    curl -O -v -k https://satellite.example.com/pub/katello-ca-consumer-latest.noarch.rpm
RUN yum install -y katello-ca-consumer-latest.noarch.rpm
RUN subscription-manager register --serverurl="satellite.example.com" --org="MyOrg" --activationkey="myKey" --force
RUN yum install -y libpng libjpeg libX11 libXext libXrender xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi
RUN yum downgrade -y openssl-libs-1.0.2k-16.el7
```

### Build base Image

> Here we're building de Dockerfile that is into "base" folder

```shell
# use add-host for add entry to /etc/hosts and resolve the DNS
$ docker build -t custom-dotnet-31-rhel7 --add-host myHost:myIP --no-cache custom
```



# Reference

- **How to register and subscribe a system to the Red Hat Customer Portal using Red Hat Subscription-Manager** - https://access.redhat.com/solutions/253273
- **Registration Assistant**  - https://access.redhat.com/labs/registrationassistant/rhel7/?tech=subscription&service=rhsm&process=offline&vdc=false&hasInsights=true&eus=1&aus=1&e4s=1