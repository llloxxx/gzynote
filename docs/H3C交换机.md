# 一、SSH配置
```bash
[H3C]local-user test class manage

[H3C-luser-manage-test]password

Password:test@123

Confirm:test@123

[H3C-luser-manage-test]authorization-attribute user-role network-operator

[H3C-luser-manage-test]authorization-attribute user-role level-15

[H3C-luser-manage-test]service-type ssh terminal

[H3C]user-interface vty 0 4

[H3C-line-vty0-4]authentication-mode scheme

[H3C-line-vty0-4]protocol inbound ssh

[H3C]ssh server enable
```
