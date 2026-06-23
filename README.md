# Render Deployment

A GitHub Action to trigger deployment in Render.

## Example Usage

```yaml
name: Trigger Render Deployment
on:
  push:
    branches:
      - main
jobs:
  main:
    name: Deploy to Render
    runs-on: ubuntu-latest
    steps:
      - name: Trigger deployment
        uses: sws2apps/render-deployment@main #consider using pin for dependabot auto update
        with:
          serviceId: ${{ secrets.RENDER_SERVICE_ID }}
          apiKey: ${{ secrets.RENDER_API_KEY }}
          multipleDeployment: false #optional, default true
```

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
