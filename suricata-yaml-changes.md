# Changes to `/etc/suricata/suricata.yaml`

Only the lines below were changed from the default Suricata 8.0.7 config.

## 1. Capture interface

The default is `eth0`, which doesn't exist on this VM, so Suricata failed to start.

```yaml
af-packet:
  - interface: enp0s3
```

Applied with:

```bash
sudo sed -i 's/interface: eth0/interface: enp0s3/' /etc/suricata/suricata.yaml
```

## 2. Load custom rules

```yaml
default-rule-path: /var/lib/suricata/rules

rule-files:
  - suricata.rules                    # ET Open ruleset from suricata-update
  - /etc/suricata/rules/local.rules   # my custom rules
```

## 3. HOME_NET

Left at the default, which already includes the lab subnet (10.0.2.0/24):

```yaml
HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
```

## Validate and apply

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v   # expect "2 rule files processed" and "successfully loaded"
sudo systemctl restart suricata
sudo grep "Engine started" /var/log/suricata/suricata.log | tail -1
```

## Useful commands

```bash
sudo tail -f -n 0 /var/log/suricata/fast.log     # watch new alerts live
sudo grep -a "capture.kernel_packets" /var/log/suricata/stats.log | tail -1   # confirm packets are captured
```
