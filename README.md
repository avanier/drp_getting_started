## Getting Started with Digital Rebar Provision for Regular Human Beings

[Digital Rebar Provision][drp_link] is a wonderful piece of software, but while
the documentation is abundant, it's definitely overwhelming for first time
users. To boot, `drpcli` is less than obvious to use. This snippet will help you
get started by walking you through the following:

- Create a new superuser
- Get a token for said user
- Set the password for said user
- Delete the default user

[drp_link]: https://rebar.digital/

## :sparkles: Magical Copy-Pasta! :spaghetti:

```bash
# drpcli expects you to configure some things with json,
# since not everything is controllable with cli flags

# Start by downloading `drpcli`
# Yes, this is VERY insecure. This is only to be used on locally hosted instances.
wget --no-check-certificate https://${endpoint_ip}:8092/files/drpcli.amd64.linux
chmod +x drpcli.amd64.linux
mv drpcli.amd64.linux ~/.local/bin/drpcli

# Let's get a sense for the default user schema
drpcli -E "https://${endpoint_ip}:8092" \
  -U 'rocketskates' \
  -P 'r0cketsk8ts' \
  users show rocketskates | jq .

# Let's craft a stub superuser of our own
read -r -d '' RD_USERDEF << EOF
{
  "Name": "$(whoami)",
  "Roles": [
    "superuser"
  ]
}
EOF

drpcli -E "https://${endpoint_ip}:8092" \
  -U 'rocketskates' \
  -P 'r0cketsk8ts' \
  users create - <<< "${RD_USERDEF}"

# We create a folder to store our token
mkdir -vp "${HOME}/.drpcli"
chmod 700 "${HOME}/.drpcli"

# Set the password for our new user
drpcli -E "https://${endpoint_ip}:8092" \
  -U 'rocketskates' \
  -P 'r0cketsk8ts' \
  users password "$(whoami)" "${my_password}"

# We get a token for the newly created user
drpcli -E "https://${endpoint_ip}:8092" \
  -U "$(whoami)" \
  -P "${my_password}" \
  users token "$(whoami)" | jq -r .Token > "${HOME}/.drpcli/token"

# Set proper permz on that token
chmod 600 "${HOME}/.drpcli/token"

# Get rid of the default user
drpcli -E "https://${endpoint_ip}:8092" \
  -T "$(cat ${HOME}/.drpcli/token)" \
  users destroy rocketskates
```

To keep going, follow the official guide from [here][guidelink].

[guidelink]: https://provision.readthedocs.io/en/latest/doc/quickstart.html#install-boot-environments-bootenvs

## Takeaways

`drpcli` is a well documented tool from an API point of view. What's not
documented is design intent, and best practice. From the snippet above, it
should be fairly obvious this is a system that is easy to automate, but harder
to bootstrap since it's not designed to be used exclusively by humans.

Happy scripting fellow sysadmins!
