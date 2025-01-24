# Zerops x Directus

This repository demonstrates how to set up and deploy Directus applications using Zerops.

![DirectusZerops](https://github.com/zeropsio/recipe-shared-assets/blob/main/covers/svg/cover-directus.svg)

[Directus](https://directus.io) is a real-time API and app dashboard that instantly transforms any SQL database into a powerful REST/GraphQL API and no-code app. This recipe showcases how to run a Directus instance on Zerops, including advanced features like schema migrations, email templating, and Object Storage integration, making it suitable for production deployments of any scale.

## Deploy on Zerops

You manually copy the [import yaml](https://github.com/zeropsio/recipe-directus/blob/main/zerops-project-import.yml) to the import dialog in the Zerops app.

```yaml
#yamlPreprocessor=on
project:
  name: recipe-directus
  tags:
    - zerops-recipe
    - headless-cms
services:
  - hostname: storage
    type: object-storage
    objectStorageSize: 2

  - hostname: redis
    type: valkey@7.2
    mode: NON_HA

  - hostname: db
    type: postgresql@16
    mode: NON_HA

  - hostname: mailpit
    type: alpine@3.20
    buildFromGit: https://github.com/zeropsio/recipe-mailpit
    enableSubdomainAccess: true

  - hostname: directus
    type: nodejs@22
    envSecrets:
      SECRET: <@generateRandomString(<32>)>
      ADMIN_EMAIL: admin@example.com
      ADMIN_PASSWORD: <@generateRandomString(<12>)>
      ADMIN_TOKEN: <@generateRandomString(<32>)>
    buildFromGit: https://github.com/zeropsio/recipe-directus
    enableSubdomainAccess: true
```

## Recipe Features

- Directus running on a load-balanced **Zerops Node.js** service
- Zerops **PostgreSQL** service as the database
- Zerops **Object Storage** (S3 compatible) service for file storage
- Automated schema migrations using Directus snapshots
- Custom email templates with Liquid templating
- Logs accessible through Zerops GUI
- Utilization of Zerops built-in **environment variables** system
- Zero downtime deployment with Zerops readiness checks
- Advanced app monitoring with health checks
- Sample collection setup with "images" demonstration

## Schema Management

Directus provides a powerful schema snapshot system for maintaining consistency across environments:

1. Make a copy of `.env.example` named `.env` and update `DB_PASSWORD` with credentials from GUI
2. Install the Zerops CLI tool `zcli` and authenticate
3. Generate a schema snapshot:
```shell
zcli vpn up
npx directus schema snapshot > snapshot.yaml
```

The recipe includes a demo "images" collection in `snapshot.yaml`. Snapshots are automatically applied during deployment via `run.initCommands`.

## Email Templates

Customize Directus email notifications by adding templates to the `./templates` directory. Templates should correspond to [base Directus templates](https://github.com/directus/directus/tree/main/api/src/services/mail/templates).

Example template structure:
```
./templates/
  └── user-invitation.liquid
```

## Project Customization

To adapt this recipe for your needs:

1. Fork this repository
2. Modify your Directus instance through the admin interface
3. Create a new snapshot using the schema snapshot command
4. Update environment variables and configurations as needed

The resulting setup will provide a ready-to-deploy Directus instance with your specific configurations.

## Additional Resources

- [Zerops Documentation](https://docs.zerops.io)
- [Directus Documentation](https://docs.directus.io)
- [Join Zerops Community](https://discord.com/invite/WDvCZ54)

Need help setting your project up? Join the [Zerops Discord community](https://discord.com/invite/WDvCZ54).