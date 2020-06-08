FROM ubuntu:18.04

RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    ca-certificates \
    libsasl2-modules \
    postfix \
    rsyslog && \
    update-ca-certificates

# Configure postfix
RUN postconf -e \
    "smtp_sasl_auth_enable = yes" \
    "smtp_sasl_security_options = noanonymous" \
    "smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd" \
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
    "relayhost = [email-smtp.us-east-1.amazonaws.com]:587" && \
    postconf -X mynetworks

# Disable kernel log, container doesn't have read permission
RUN sed --in-place 's/\(module(load="imklog" permitnonkernelfacility="on")\)/# \1/' /etc/rsyslog.conf

CMD cp /etc/resolv.conf /var/spool/postfix/etc/ && \
    cp /etc/services /var/spool/postfix/etc/ && \
    echo "[email-smtp.us-east-1.amazonaws.com]:587 $SES_USERNAME:$SES_PASSWORD" > /etc/postfix/sasl_passwd && \
    postmap hash:/etc/postfix/sasl_passwd && \
    chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db && \
    chmod 600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db && \
    service rsyslog start && \
    postfix start-fg