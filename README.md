# Zerops x Directus

This repository demonstrates how to set up and deploy Directus applications using Zerops.

![DirectusZerops](https://github.com/zeropsio/recipe-shared-assets/blob/main/covers/svg/cover-directus.svg)

[Directus](https://directus.io) is a real-time API and app dashboard that instantly transforms any SQL database into a powerful REST/GraphQL API and no-code app. This recipe showcases how to run a Directus instance on Zerops, including advanced features like schema migrations, email templating, and Object Storage integration, making it suitable for production deployments of any scale.

## Deploy on Zerops

You can either click the deploy button to deploy development setup directly on Zerops or manually copy
the [import yaml](https://github.com/zeropsio/recipe-directus/blob/main/zerops-project-import.yml) to the
import dialog in the Zerops app.

[![Deploy on Zerops](https://github.com/zeropsio/recipe-shared-assets/blob/main/deploy-button/green/deploy-button.svg)](https://app.zerops.io/recipe/directus)

<br/>


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

## Production Deployment Guidelines

#### Database Layer
- Enable **High Availability** mode for PostgreSQL service
- Configure appropriate backup strategies

#### Cache Layer
- Upgrade Redis to **HA mode** for enhanced reliability
- Zerops HA Redis (Valkey) implementation automatically handles master/replica routing:
    - Write operations are directed to master
    - Read operations are distributed across all nodes
    - No manual sentinel configuration required

### Multi-Container Setup

When scaling Directus across multiple containers, implement these adjustments:

1. **Redis Configuration**

See: https://docs.directus.io/self-hosted/config-options.html#redis

### Email Configuration

For production environments:
1. Replace the development `mailpit` service with a production-ready email service
2. Configure appropriate email credentials in environment variables
3. Verify email template rendering and delivery

### Schema Management

Implement robust schema version control:

```bash
# Wrap schema applications in execOnce to prevent duplicate migrations on multiple containers.
zsc execOnce "migrate-${appVersionId}" -- directus schema apply --yes snapshot.yaml
```

This approach ensures:
- One-time execution of migrations
- Version-specific schema updates
- Reliable deployment across environments

### Pre-Production Checklist

- [ ] Enable HA mode for PostgreSQL
- [ ] Configure HA Redis
- [ ] Remove development services (e.g., mailpit)
- [ ] Configure production email service
- [ ] Review and update environment variables
- [ ] Test schema migration process
- [ ] Verify backup procedures
- [ ] Review security settings

> **Note**: These guidelines represent baseline production considerations. Additional optimizations may be necessary based on specific use cases and requirements.

## Additional Resources

- [Zerops Documentation](https://docs.zerops.io)
- [Directus Documentation](https://docs.directus.io)
- [Join Zerops Community](https://discord.com/invite/WDvCZ54)

Need help setting your project up? Join the [Zerops Discord community](https://discord.com/invite/WDvCZ54).
