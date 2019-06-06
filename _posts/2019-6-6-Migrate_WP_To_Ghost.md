---
title: How to migrate from Wordpress to Ghost 2?
permalink: /blog/2019/06/06/migrate_wp_to_ghost/
categories:
  - Uncategorized
published: true
---

During my (trial) move to Ghost v2 I had to migrate all of my posts written in Wordpress. I could easily export them from WP but importing them into Ghost was the difficult part. This is because there is no official plugin to do the export. There is one, but it only supports Ghost 1.

So what is the solution?

You have to install Ghost 1, do the import and upgrade. But not everyone can do that on a hosted Ghost or their VPS. I was looking for a workable solution until I found this. Assuming a little bit knowledge of Docker, it gives you a simple way of doing the export. Basically, you have to use Ghost exporter plugin for WP to export your posts to Ghost 1 format, set up a Ghost 1 docker, import that file, destroy the Ghost 1 docker (but keep its contents), set up a Ghost 2 docker based on same contents source (at which point you will have all your posts in Ghost 2). Then you export from Ghost 2 docker and import in your VPS/Hosted ghost.

Below are steps in more detail:

Install Ghost export for WP plugin.
In your WP console, use the plugin to export your posts. You will receive the export in a json file named ghost.json
Create a directory on your computer to store Ghost's contents (e.g. /path/to/ghost/blog).
Run below command to start a Docker based on Ghost v1 docker image:

```bash
docker run -d \
	--name some-ghost \
	-p 3001:2368 \
	-v /path/to/ghost/blog:/var/lib/ghost/content \
	ghost:1-alpine
```

5. At this point, browse http://localhost:3001/ to see your newly set up Ghost.

6. Register on your local Ghost and import content from ghost.json you received on step 2.

7. Remove your Ghost docker by docker rm -f some-ghost

8. Start a new Ghost docker by running below command:

```bash
docker run -d \
	--name some-ghost \
	-p 3001:2368 \
	-v /path/to/ghost/blog:/var/lib/ghost/content \
	ghost:2-alpine
```

9. Again, go to http://localhost:3001/ to have a fresh Ghost 2 installation.

10. Register and log in. You will have all your posts in Ghost 2 format.

11. Export from Ghost to a new ghost.json but this time in Ghost 2 format.

12. Import ghost.json from step 11 into your hosted Ghost.

