### Important Notice

From `nodejs14.x`, AWS Lambda doesn't bundle `ImageMagick` and image related libraries.

## Configuration

Configuration file you will find under the name `config.json` in project root.

```json
{
  "bucket": "your-destination-bucket",
  "backup": {
      "directory": "./original"
  },
  "reduce": {
      "directory": "./reduced",
      "prefix": "reduced-",
      "quality": 90,
      "acl": "public-read",
      "cacheControl": "public, max-age=31536000"
  },
  "resizes": [
    {
      "size": 300,
      "directory": "./resized/small",
      "prefix": "resized-",
      "cacheControl": null
    },
    {
      "size": 450,
      "directory": "./resized/medium",
      "suffix": "_medium"
    },
    {
      "size": "600x600^",
      "gravity": "Center",
      "crop": "600x600",
      "directory": "./resized/cropped-to-square"
    },
    {
      "size": 600,
      "directory": "./resized/600-jpeg",
      "format": "jpg",
      "background": "white"
    },
    {
      "size": 900,
      "directory": "./resized/large",
      "quality": 90
    }
  ]
}
```

## Installation

### Setup

To use the automated deployment scripts you will need to have
[aws-cli installed and configured](http://docs.aws.amazon.com/cli/latest/userguide/installing.html).

Deployment scripts are pre-configured to use some default values for the Lambda
configuration. I you want to change any of those just use:

```bash
npm config set aws-lambda-image:profile default
npm config set aws-lambda-image:region eu-west-1
npm config set aws-lambda-image:memory 1280
npm config set aws-lambda-image:timeout 5
npm config set aws-lambda-image:name lambda-function-name
npm config set aws-lambda-image:role lambda-execution-role
```

### Deployment

Command below will deploy the Lambda function on AWS, together with setting up
roles and policies.

```bash
npm run deploy
```

*Notice*: Because there are some limitations in `Claudia.js` support for
policies, which could lead to issues with `Access Denied` when processing
images from one bucket and saving them to another, we have decided to introduce
support for custom policies.

### Adding S3 event handlers

To complete installation process you will need to take one more action. It will
allow you to install S3 Bucket event handler, which will send information about
all uploaded images directly to your Lambda function.

```bash
npm run add-s3-handler --s3_bucket="your-bucket-name" --s3_prefix="directory/" --s3_suffix=".jpg"
```

You are able to install multiple handlers per Bucket. So, to add handler for PNG
files you just need to re-run above command with different _suffix_, ie:

```bash
npm run add-s3-handler --s3_bucket="your-bucket-name" --s3_prefix="directory/" --s3_suffix=".png"
```

### Updating

To update Lambda with you latest code just use command below. Script will build
new package and automatically publish it on AWS.

```bash
npm run update
```
