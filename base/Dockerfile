FROM registry.redhat.io/dotnet/dotnet-31-rhel7

ARG  USERNAME
ARG  PASSWORD

USER 0

RUN subscription-manager register --username ${USERNAME} --password ${PASSWORD} --auto-attach && \
	yum update -y && \
	yum repolist