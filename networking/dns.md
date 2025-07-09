# DNS (Domain Name System)

The phonebook of the internet. While humans use domain names (like `example.com`), computers use **IP addresses** (like `93.184.216.34`) to locate resources. DNS translates human-readable domain names into machine-readable IP addresses and vice versa.

## How DNS Works

1. User enters a domain name into the browser.
2. **DNS Resolver** (usually provided by the ISP or a public DNS like Google 8.8.8.8) is queried.
3. The resolver checks if the IP is cached.
4. If not, it contacts:
   * **Root DNS server** → tells where `.com` DNS servers are.
   * **TLD (Top Level Domain) DNS server** → tells where `example.com` DNS servers are.
   * **Authoritative DNS server** → provides the actual IP for `example.com`.

> DNS typically uses **UDP** on **port 53**, but can use **TCP** for zone transfers or large queries.

## DNS Records

DNS records (also known as **Resource Records or RRs**) are entries in DNS that map domain names to data such as IP addresses, mail servers, etc. They are stored in **zone files** on **authoritative DNS servers**.

### Main Types

| Record                                      | What it does                                                    | Note                                                     |
| ------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| A (IPv4 Address)                            | Map: domain name → IPv4 address                                 | Most common DNS record.                                  |
| AAAA (IPv6 Address)                         | Map: domain name → IPv6 address                                 | Supports IPv6-enabled networks.                          |
| CNAME (Canonical Name)                      | Aliases one domain to another. Used for subdomains.             | Cannot coexist with other records for the same name.     |
| MX (Mail Exchange)                          | Specifies mail servers for receiving email for the domain.      | Has priority → lower value = higher priority.            |
| NS (Name Server)                            | Specifies DNS servers for the domain.                           | Delegates DNS resolution.                                |
| PTR (Pointer Record)                        | Used for reverse DNS lookup.                                    | Stored under `in-addr.arpa` (IPv4) or `ip6.arpa` (IPv6). |
| SOA (Start of Authority)                    | Stores admin details and zone control parameters.               | One SOA per zone.       |
| TXT (Text Record)                           | Stores text. Used for SPF, DKIM, DMARC.         | Used for verification, mail policies, and metadata.      |

### Other Types

| Record                                      | What it does                                                    | Note                                                     |
| ------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| SRV (Service Record)                        | Specifies services (protocol, port, host).                      | Used in VoIP, SIP, LDAP, etc.                            |
| CAA (Certification Authority Authorization) | Specifies which CAs can issue certificates for the domain.      | Helps prevent unauthorized SSL certs.                    |
| NAPTR (Name Authority Pointer)              | Supports complex service discovery (like ENUM).                 | Often used with SRV records.                             |
| RRSIG (DNSSEC Signature)                    | Contains cryptographic signature for a record set.              | Used in DNSSEC to verify authenticity.                   |
| DNSKEY                                      | Public key used to verify DNSSEC signatures.                    | Stored with the zone and referenced by DS.               |
| DS (Delegation Signer)                      | Points to child DNSKEY in the parent zone.                      | Used for DNSSEC chain of trust.                          |
| NSEC / NSEC3                                | Proof of non-existence of a DNS record.                         | Used to securely say "this name does not exist".         |
| CDNSKEY / CDS                               | Used to publish child DNSSEC keys to parent.                    | Helps automate DNSSEC setup.                             |
| DNAME (Delegation Name)                     | Like CNAME, but for entire sub-tree of DNS.                     | Redirects all names under a domain to another.           |
| TLSA                                        | Associates TLS certificates with domain names (used with DANE). | Works with DNSSEC to secure SSL/TLS.                     |

## 🧩 Additional Concepts

1. **Zone** --> Part of a DNS namespace, managed by a particular org. A domain is a zone (subdomains can also be their zones if defined).
    * *Zone File* --> Stores all the records for a zone. SOA then NS then A, MX, TXT etc.
2. **TTL** (Time To Live) --> Defines how long DNS records are cached. It uses seconds ( `3600` = 1 hour).
3. **DNS Propagation** --> Changes in records take time to update everywhere (because of caching).
4. **Authoritative DNS Server** --> Holds a domain's actual DNS records and managed by us via a hosting provider. It doesn't cache.
5. **Recursive DNS Resolver** --> Resolver that end-users/apps talk to.
    * How it works
        1. Checks cache.
        2. Asks the root servers "who handles `<TLD>`?"
        3. Asks the TLD servers "who handles `<domain>.<TLD>`?"
        4. Asks the authoritative DNS "what's the IP for `<domain>.<TLD>`?"
        5. Gets the final answer (A record's IP) and gets it to user.

### Email Auth Protocols

There are 3 main ones. They all use the format of "version tag + protocol". Version Tag is absolutely necessary --> `v=spf1`, `v=DKIM1` or `v=DMARC1`.

| Name | Type | TTL | Value |
| --- | --- | --- | --- |
| @ | TXT | 3600 | `"v=spf1 include:_spf.google.com ~all"` |
| s1._domainkey | TXT | 3600 | `"v=DKIM1; k=rsa; p=<public-key>"` |
| _dmarc | TXT | 3600 | `"v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com; adkim=s; aspf=s"` |

1. **SPF** (Sender Policy Framework) --> List of authorized senders (who can send emails on behalf of your domain).
    * *Mechanisms* --> They match the email
        * `mx` matches against domain's MX records.
        * `a` matches against domain's A records.
        * `ip4` and `ip6` matches for specified IPv4/IPv6 addresses.
        * `include:` delegates checking to specified domain's SPF record.
        * `all` The fallback statement at the end and decides what to do when previous statements don't get matched by email.
    * *SPF Qualifiers* --> `+` (Pass) Allow, `-` (Fail) Reject, `~` (Softfail) Mark Suspicious and `?` (Neutral) IDK.
2. **DKIM** (DomainKeys Identified Mail) --> For Data Authenticity and Integrity (with digital signature). These are provided by ESP.
    * `k=rsa` defines that the algorithm is RSA.
    * How it works
        1. Sender's "MailServer" generates a hash from the email body and creates a *hash* which is then encrypted using the *private-key*.
        2. The encrypted-hash (and other metadata) goes inside the *DKIM Signature* which is added to the email.
        3. Reciever's "MailServer" gets the email and DNS queries (on the domain defined in *DKIM Signature*) to get the *public-key* from DNS TXT DKIM record.
        4. It uses this public-key to decrypt the encrypted-hash and matches with a hash that it recomputes from the email.
        5. If both hashes match then the email is genuine.
3. **DMARC** (Domain-based Message Authentication, Reporting and Conformance) --> Tells what to do incase both/either *SPF* and/or *DKIM* fail.
    * *Parameters*
        * `p=reject` Policy is to Reject emails that fail.
        * `rua=mailto:...` Where to send reports.
        * `adkim=s` Strict alignment for DKIM.
        * `aspf=s` Strict alignment for SPF.

---

### DNS Tools

1. Terminal
    * `dig` (Linux/macOS): `dig example.com ANY`
    * `nslookup` (Windows)
2. Online tools:
    * [dnschecker.org](https://dnschecker.org)
    * [mxtoolbox.com](https://mxtoolbox.com)
    * [whois.domaintools.com](https://whois.domaintools.com)
