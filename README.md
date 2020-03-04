# jq-lambda

jq lambda layer

## Layers

| Region    | ARN                                                     |
|-----------|---------------------------------------------------------|
| us-east-1 | arn:aws:lambda:us-east-1:961053664803:layer:jq-lambda:2 |

Uses jq version 1.6 as of 2020-03-03.

## Developing

Since `jq` is written in portable C, this is actually really easy.

```bash
link="$(curl -s https://api.github.com/repos/stedolan/jq/releases/latest | jq '.assets[] | if .name == "jq-linux64" then . else empty end | .browser_download_url' | tr -d \")"
wget $link -O jq
chmod +x jq
zip jq-lambda jq
```

Now use the AWS CLI to upload a new lambda layer. Choose the desired region.
```bash
# Still in output/
export REGION="us-east-1"
aws lambda publish-layer-version \
        --region "$REGION" \
        --layer-name jq-lambda \
        --description "jq lambda layer" \
        --zip-file fileb://jq-lambda.zip
# Make public
# From https://github.com/developmentseed/geolambda#create-a-new-version
aws lambda add-layer-version-permission \
    --region "$REGION" \
    --layer-name jq-lambda \
	--statement-id public \
    --version-number 2 \
    --principal '*' \
	--action lambda:GetLayerVersion
```
