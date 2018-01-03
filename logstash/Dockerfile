# escape=`
FROM openjdk:8-jdk-windowsservercore
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Logstash
ENV ES_VERSION="6.1.1" `
    ES_SHA1="a2a420169dff31f3d5d65628a9d1eaad930a8e544fc4e986e3671ffeea3a1d03c8f77f8b51568a0da082588a843568fb7aefe29c1c90365882f429f9536f9240" `
    ES_HOME="C:\logstash"

RUN Invoke-WebRequest -outfile logstash.zip "https://artifacts.elastic.co/downloads/logstash/logstash-$($env:ES_VERSION).zip" -UseBasicParsing; `
    #if ((Get-FileHash elasticsearch.zip -Algorithm sha1).Hash -ne $env:ES_SHA1) {exit 1} ; `
    Expand-Archive logstash.zip -DestinationPath C:\ ; `
    Move-Item c:/logstash-$($env:ES_VERSION) $env:ES_HOME; `
    Remove-Item logstash.zip

VOLUME C:\data

# REMARKS - DNS and mount tweaks needed for Windows
RUN Set-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name ServerPriorityTimeLimit -Value 0 -Type DWord; `
    Set-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\DOS Devices' -Name 'G:' -Value '\??\C:\data' -Type String    

#HEALTHCHECK --interval=5s `
# CMD powershell -command `
#    try { `
#     $content = (iwr -useb http://localhost:9200/_cat/health).Content; `
#     $health = $content.Split(' ')[3]; `
#     if ($health -eq 'green' -or $health -eq 'yellow') { return 0 } `
#     else { return 1 }; `
#    } catch { return 1 }
    
#EXPOSE 9200 9300
WORKDIR C:\logstash
#COPY config ./config

SHELL ["cmd", "/S", "/C"]
CMD ".\bin\logstash.bat"