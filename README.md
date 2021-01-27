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



# Reference

- **How to register and subscribe a system to the Red Hat Customer Portal using Red Hat Subscription-Manager** - https://access.redhat.com/solutions/253273
- **Registration Assistant**  - https://access.redhat.com/labs/registrationassistant/rhel7/?tech=subscription&service=rhsm&process=offline&vdc=false&hasInsights=true&eus=1&aus=1&e4s=1