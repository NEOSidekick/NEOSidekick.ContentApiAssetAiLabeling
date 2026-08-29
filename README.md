# Content API Asset AI Labeling

> **Beta:** This package is under active development.

Optional Content API adapter for
`neosidekick/media-ui-asset-ai-labeling`. It adds the asset's AI classification
to Networkteam Content API output without coupling the base Media UI package to
Content API dependencies.

## Zebra integration

Install this package when the Neos website uses Zebra and its frontend needs to
render disclosures for assets marked as `AI-generated` or `AI-modified`:

```bash
composer require neosidekick/content-api-asset-ai-labeling
```

The package adds `aiClassification` and Schema.org `digitalSourceType` fields
to:

- images rendered through `Networkteam.Neos.Util:ImageUriAndDimensions`
- generic assets serialized by `Networkteam.Neos.ContentApi:Properties`

The value is `AI-generated`, `AI-modified`, or `null`. Image variants inherit
the classification of their original image, and generic assets also support
videos.

An image returned to Zebra can therefore look like this:

```json
{
  "src": "https://example.test/_Resources/Persistent/image.jpg",
  "width": 1600,
  "height": 900,
  "aiClassification": "AI-modified",
  "digitalSourceType": "https://schema.org/CompositeWithTrainedAlgorithmicMediaDigitalSource"
}
```

The frontend can render the supplied Schema.org value without duplicating the
classification mapping:

```tsx
export function ClassifiedImage({ image, alternativeText }) {
  return (
    <figure itemScope itemType="https://schema.org/ImageObject">
      <img
        src={image.src}
        width={image.width}
        height={image.height}
        alt={alternativeText}
        itemProp="contentUrl"
      />
      {image.digitalSourceType && (
        <meta itemProp="digitalSourceType" content={image.digitalSourceType} />
      )}
    </figure>
  );
}
```

## Requirements

- Neos 8.4
- `neosidekick/media-ui-asset-ai-labeling` `dev-main`
- `networkteam/neos-contentapi` 0.8.x
- `networkteam/neos-util` 8.1+
- PHP 8.3

## Data contract

The field uses the stable media tag labels. Zebra frontends can map these values
to localized disclosure text without exposing all Neos Media tags.
