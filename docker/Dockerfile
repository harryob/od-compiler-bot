FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS builder

WORKDIR /opendream

COPY ./OpenDream/ .

ARG BULD_CONFIG=Release

RUN if [ "$BULD_CONFIG" = "Release" ]; then \
		apk update && apk add tar && mkdir -p /opendream/bin/DMCompiler && mkdir -p /opendream/bin/Content.Server && \
		curl -L https://github.com/OpenDreamProject/OpenDream/releases/download/latest/DMCompiler_linux-x64.tar.gz -o DMCompiler.tar.gz && \
		curl -L https://github.com/OpenDreamProject/OpenDream/releases/download/latest/OpenDreamServer_linux-x64.tar.gz -o Content.Server.tar.gz && \
		tar x -z -f DMCompiler.tar.gz -C /opendream/bin/DMCompiler --strip-components=1 && tar x -z -f Content.Server.tar.gz -C /opendream/bin/Content.Server --strip-components=1; \
	else \
		dotnet build OpenDreamServer --configuration ${BULD_CONFIG} && \
		dotnet build DMCompiler --configuration ${BULD_CONFIG}; \
	fi && \
	ls /opendream/bin && \
	ln -s /opendream/bin/DMCompiler/DMCompiler /usr/bin/ && \
	ln -s /opendream/bin/Content.Server/OpenDreamServer /usr/bin;

FROM builder

WORKDIR /app

COPY docker/run.sh .

RUN adduser odcompile -H -D && \
	chown -R odcompile: /app && \
	chown -R odcompile: /opendream/bin

USER odcompile

ENTRYPOINT [ "/bin/sh", "/app/run.sh" ]
