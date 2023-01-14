
If you find that there is too much information, you can either use the `--format` option to the docker command or pipe the output to the `jq` command to extract only what you want.

```
> docker inspect --format='{{.NetworkSettings.IPAddress}}' mymongo
> docker inspect mymongo | jq .[0].NetworkSettings.IPAddress
```
