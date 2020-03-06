FROM ubuntu:18.04

RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    ca-certificates \
    libsasl2-modules \
    postfix \
    rsyslog && \
    update-ca-certificates

# Configure postfix
RUN postconf -e \
    "smtp_use_tls = yes" \
    "smtp_tls_security_level = encrypt" \
    "smtp_tls_note_starttls_offer = yes" \
    "smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt" \
    "recipient_delimiter = +" \
    "unknown_local_recipient_reject_code = 550" \
    "owner_request_special = no" \
    "transport_maps = regexp:/opt/mailman/core/var/data/postfix_lmtp" \
    "local_recipient_maps = regexp:/opt/mailman/core/var/data/postfix_lmtp" \
    "relay_domains = regexp:/opt/mailman/core/var/data/postfix_domains" \
    "mydomain = lists.cs50.harvard.edu" \
    "myhostname = lists.cs50.harvard.edu" \
    "mynetworks_style = subnet" \
    "relayhost = relay.huit.harvard.edu:25" && \
    postconf -X mynetworks

CMD service rsyslog start && \
    postfix start-fg
