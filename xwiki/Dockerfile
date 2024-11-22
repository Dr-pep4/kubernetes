
# ---------------------------------------------------------------------------
    FROM tomcat:9-jre17


    LABEL org.opencontainers.image.authors='XWiki Development Team <committers@xwiki.org>'
    LABEL org.opencontainers.image.url='https://hub.docker.com/_/xwiki'
    LABEL org.opencontainers.image.documentation='https://hub.docker.com/_/xwiki'
    LABEL org.opencontainers.image.source='https://github.com/xwiki/xwiki-docker.git'
    LABEL org.opencontainers.image.vendor='xwiki.org'
    LABEL org.opencontainers.image.licenses='LGPL-2.1'
    

    RUN apt-get update && \
      apt-get --no-install-recommends -y install \
        curl \
        libreoffice \
        unzip \
        procps \
        libpostgresql-jdbc-java && \
      rm -rf /var/lib/apt/lists/*
    
    ENV XWIKI_VERSION=15.10.6
    ENV XWIKI_URL_PREFIX="https://maven.xwiki.org/releases/org/xwiki/platform/xwiki-platform-distribution-war/${XWIKI_VERSION}"
    ENV XWIKI_DOWNLOAD_SHA256=316822e098d5cac01b1d4504974f018656b92809
    RUN rm -rf /usr/local/tomcat/webapps/* && \
      mkdir -p /usr/local/tomcat/temp && \
      mkdir -p /usr/local/xwiki/data && \
      curl -fSL "${XWIKI_URL_PREFIX}/xwiki-platform-distribution-war-${XWIKI_VERSION}.war" -o xwiki.war && \
      unzip -d /usr/local/tomcat/webapps/ROOT xwiki.war && \
      rm -f xwiki.war
    
    # Copy the JDBC driver in the XWiki webapp
    RUN cp /usr/share/java/postgresql-jdbc4.jar /usr/local/tomcat/webapps/ROOT/WEB-INF/lib/
    

    COPY tomcat/setenv.sh /usr/local/tomcat/bin/
    COPY tomcat/server.xml /usr/local/tomcat/bin/
    
    COPY xwiki/htmlheader.vm /usr/local/tomcat/webapps/ROOT/skins/flamingo/
    COPY xwiki/javascript.vm /usr/local/tomcat/webapps/ROOT/skins/flamingo/
    COPY xwiki/hibernate.cfg.xml /usr/local/tomcat/webapps/ROOT/WEB-INF/hibernate.cfg.xml

    RUN sed -i 's/<id>org.xwiki.platform:xwiki-platform-distribution-war/<id>org.xwiki.platform:xwiki-platform-distribution-docker/' \
      /usr/local/tomcat/webapps/ROOT/META-INF/extension.xed

    COPY xwiki/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
    
    VOLUME /usr/local/xwiki
    
    RUN echo "done"
 
    ENTRYPOINT ["docker-entrypoint.sh"]
    CMD ["xwiki"]
