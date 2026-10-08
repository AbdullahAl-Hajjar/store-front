# Store Front

Part of the Algonquin Pet Store (CST8915). A Vue.js 3 single-page app where customers pick a product, enter a quantity and place an order. It loads products from the product-service and sends orders to the order-service.

## Configuration (12-Factor: Config)

The two API base URLs come from environment variables that start with `VUE_APP_`:

| Variable | Example |
| --- | --- |
| `VUE_APP_PRODUCT_SERVICE_URL` | `https://product-service-app-aa.azurewebsites.net` |
| `VUE_APP_ORDER_SERVICE_URL` | `https://order-service-app-aa.azurewebsites.net` |

Use the base URL without a trailing slash, because the code appends `/products` and `/orders`.

Unlike the backend services, these values are **baked into the JavaScript bundle at build time**. The built app is just static files running in the user's browser, so it can't read server environment variables at runtime. Changing a URL means rebuilding. The values are visible to anyone who opens the site, so only public API URLs belong here.

## Run locally

```bash
cp .env.example .env    # then edit the URLs
npm ci
npm run serve
```

## Deployment (Lab 3)

`.github/workflows/azure-app-service-store-front.yml` runs on every push to `main`. It declares the two `VUE_APP_` variables in an `env:` block on `build_and_deploy_job`, runs `npm ci` and `npm run build` with them, and deploys `dist/` to the Linux Web App `store-front-app-aa`, which serves the files with `pm2 serve --spa`.

The lab asks for Azure Static Web Apps, but my Azure for Students subscription only allows a few regions and Static Web Apps isn't offered in any of them (every attempt failed with `RequestDisallowedByAzure`). App Service plus GitHub Actions gives the same result: CI/CD from GitHub, with the service URLs baked in at build time.

The workflow needs one repository secret, `AZURE_WEBAPP_PUBLISH_PROFILE`, which holds the Web App's publish profile.
