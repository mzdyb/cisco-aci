---
tenants:
  - name: Production
    description: 'Production Tenant'
  - name: Developement
    description: 'Developement Tenant'

vrfs:
  - vrf_name: prod_vrf1
    description: 'Production vrf1'
    tenant: Production
    policy_direction: ingress
    policy_enforcement: enforced
  - vrf_name: prod_vrf2
    description: 'Production vrf2'
    tenant: Production
    policy_direction: ingress
    policy_enforcement: enforced

bridge_domains:
  - bd_name: prod_bd1_10.0.0.1_24
    tenant: Production
    vrf: prod_vrf1
    subnets:
      - gateway: 10.0.0.1
        mask: 24
        scope: public
  - bd_name: prod_bd2_10.10.0.1_24
    tenant: Production
    vrf: prod_vrf1
    subnets:
      - gateway: 10.10.0.1
        mask: 24
        scope: public

application_profiles:
  - ap_name: ap1
    tenant: Production

epgs:
  - epg_name: epg1
    tenant: Production
    ap: ap1
    epg: epg1
    bd: prod_bd1_10.0.0.1_24
    

filters:
  - filter_name: web_filter
    tenant: Production
    description: 'Web filter'
    filter_entries:
      - entry_name: 'allow_https'
        ethertype: ip
        protocol: tcp
        dst_port: 443
      
