# Shopifyテーマを作成する

## 自作ページの追加

chocolat lumièreオンラインショップでは、`About Usページ`のみオリジナルページとして実装されています。

## templateディレクトリに page.aboutus.json を作成

```json:page.aboutus.json
{
    "sections": {
        "main": {
            "type": "main-page",
            "settings": {
                "padding_top": 36,
                "padding_bottom": 36
            }
        }
    },
    "order": ["main"]
}
```

## セクションを作成

## `sections`ディレクトリに `members.liquid` と`twitter-feed.liquid`を作成

<details>
    <summary>コード全文</summary>

```liquid:members.liquid
<link rel="stylesheet" href="{{ 'photoswipe.css' | asset_url }}" media="print" onload="this.media='all'">
<link
  rel="stylesheet"
  href="{{ 'photoswipe-dynamic-caption-plugin.css' | asset_url }}"
  media="print"
  onload="this.media='all'"
>

<noscript>{{ 'photoswipe.css' | asset_url | stylesheet_tag }}</noscript>
<noscript>{{ 'photoswipe-dynamic-caption-plugin.css' | asset_url | stylesheet_tag }}</noscript>

{% style %}
  div:empty {
    display: block;
  }
  .pswp__bg {
    display: block;
  }
  :root{
    --lightbox-padding: 0.5rem;
    --lightbox-width: calc(100% / 2 - var(--lightbox-padding) * 4);
  }
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
  }
  @media screen and (min-width: 750px) {
    :root{
      --lightbox-width: 20vw;
    }
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
  }
  .section-member-heading {
    text-align: center;
  }
  .lightbox-card-container {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
  }
  .lightbox-card {
    width: var(--lightbox-width);
    height: auto;
    flex-shrink: 0;
    padding: var(--lightbox-padding);
  }
  .thumbnail-image {
    width: 100%;
    height: auto;
    border-radius: 0.5rem;
  }
  .twitter {
    color: #1da1f2;
  }
  .instagram path {
    fill: url(#instagramGradient);
    color: #F56040;
  }
  .tiktok {
    color: #1E3050;
  }
{% endstyle %}

{% comment %} svg of instagram gradient {% endcomment %}
<svg width="0" height="0">
  <radialGradient id="instagramGradient" r="150%" cx="30%" cy="107%">
    <stop stop-color="#fdf497" offset="0" />
    <stop stop-color="#fdf497" offset="0.05" />
    <stop stop-color="#fd5949" offset="0.45" />
    <stop stop-color="#d6249f" offset="0.6" />
    <stop stop-color="#285AEB" offset="0.9" />
  </radialGradient>
</svg>

<div class="color-{{ section.settings.color_scheme }} gradient">
  <div class="page-width section-{{ section.id }}-padding">
    {%- if section.blocks != blank -%}
      {% if section.blocks.size <= 1 and section.blocks[0].settings.image == blank %}
        <h3 class="section-member-heading {{ section.settings.heading_size }}">1期生</h3>
        <div id="lightbox-container" class="lightbox-card-container">
          {% for index in (1..4) %}
            {% liquid
              assign image_src = 'blank_image'
              assign image_width = 1300
              assign image_height = 731
            %}
            <div class="lightbox-card placeholder-border">
              <a
                href="{{ image_src | file_img_url: 'master' }}"
                class="lightbox-content"
                data-pswp-width="{{ image_width }}"
                data-pswp-height="{{ image_height }}"
                target="_blank"
              >
                <div class="thumbnail-image">
                  {{ 'hero-apparel-2' | placeholder_svg_tag: 'placeholder-svg' }}
                </div>
              </a>
              <span>メンバー{{ index }}</span>
            </div>
          {%- endfor -%}
        </div>
      {% else %}
        <h3 class="section-member-heading {{ section.settings.heading_size }}">
          {{ section.settings.title | escape }}
        </h3>
        <div id="lightbox-container" class="lightbox-card-container">
          {% for block in section.blocks %}
            {% liquid
              assign image_src = block.settings.image | split: '/' | last
              assign image_width = block.settings.image.width
              assign image_height = image_width | divided_by: block.settings.image.aspect_ratio | round
            %}
            {%- capture social_icons -%}
        {% if block.settings.twitter != blank %}
        <a target="_blank" href="{{ block.settings.twitter }}"><i class="fa-brands fa-twitter twitter"></i></a>
        {% endif %}
        {% if block.settings.instagram != blank %}
        <a target="_blank" href="{{ block.settings.instagram }}"><i class="fa-brands fa-instagram instagram"></i></a>
        {% endif %}
        {% if block.settings.tiktok != blank %}
        <a target="_blank" href="{{ block.settings.tiktok }}"><i class="fa-brands fa-tiktok tiktok"></i></a>
        {% endif %}
      {%- endcapture -%}
            <div class="lightbox-card">
              <a
                href="{{ image_src | file_img_url: 'master' }}"
                class="lightbox-content"
                data-pswp-width="{{ image_width }}"
                data-pswp-height="{{ image_height }}"
                target="_blank"
              >
                {{
                  block.settings.image
                  | image_url: width: image_width
                  | image_tag:
                    class: 'thumbnail-image',
                    loading: 'lazy',
                    width: image_width,
                    height: image_height,
                    alt: block.settings.name
                  | escape
                }}
              </a>
              <div class="pswp-caption-content">
                {{ block.settings.description | newline_to_br }}
                <p>{{ social_icons }}</p>
              </div>
              <span>{{ block.settings.name | escape }}</span>
              {{ social_icons }}
            </div>
          {%- endfor -%}
        </div>
      {%- endif -%}
    {% endif %}
  </div>
</div>

{% javascript %}
  const maxWdith = 750;

  const smallScreenPadding = {
    top: 0, bottom: 0, left: 0, right: 0
  };
  const largeScreenPadding = {
    top: 30, bottom: 30, left: 0, right: 0
  };
  const lightbox = new PhotoSwipeLightbox({
    gallery: '#lightbox-container',
    children: '.lightbox-card',
    pswpModule: PhotoSwipe,
    zoom: false,
    paddingFn: (viewportSize) => {
      return viewportSize.x < maxWdith ? smallScreenPadding : largeScreenPadding
    },
  });
  const captionPlugin = new PhotoSwipeDynamicCaption(lightbox, {
    type: 'auto',
    mobileLayoutBreakpoint: maxWdith,
    mobileCaptionOverlapRatio: 1,
  });
  lightbox.init();
{% endjavascript %}

{% schema %}
{
  "name": "メンバーリスト",
  "tag": "section",
  "class": "section",
  "disabled_on": {
    "groups": [
      "header",
      "footer"
    ]
  },
  "settings": [
    {
      "id": "title",
      "type": "text",
      "label": "見出し",
      "default": "1期生"
    },
    {
      "type": "select",
      "id": "color_scheme",
      "options": [
        {
          "value": "accent-1",
          "label": "t:sections.all.colors.accent_1.label"
        },
        {
          "value": "accent-2",
          "label": "t:sections.all.colors.accent_2.label"
        },
        {
          "value": "background-1",
          "label": "t:sections.all.colors.background_1.label"
        },
        {
          "value": "background-2",
          "label": "t:sections.all.colors.background_2.label"
        },
        {
          "value": "inverse",
          "label": "t:sections.all.colors.inverse.label"
        }
      ],
      "default": "background-1",
      "label": "t:sections.all.colors.label"
    },
    {
      "type": "select",
      "id": "heading_size",
      "options": [
        {
          "value": "h2",
          "label": "t:sections.all.heading_size.options__3.label"
        },
        {
          "value": "h3",
          "label": "t:sections.all.heading_size.options__2.label"
        },
        {
          "value": "h4",
          "label": "t:sections.all.heading_size.options__1.label"
        }
      ],
      "default": "h3",
      "label": "t:sections.all.heading_size.label"
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "t:sections.all.padding.padding_top",
      "default": 8
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "t:sections.all.padding.padding_bottom",
      "default": 8
    }
  ],
  "blocks": [
    {
      "type": "image",
      "name": "画像",
      "settings": [
        {
          "id": "image",
          "type": "image_picker",
          "label": "画像を選択"
        },
        {
          "id": "name",
          "type": "text",
          "label": "名前"
        },
        {
          "id": "description",
          "type": "textarea",
          "label": "説明"
        },
        {
          "id": "twitter",
          "type": "url",
          "label": "Twitter"
        },
        {
          "id": "instagram",
          "type": "url",
          "label": "Instagram"
        },
        {
          "id": "tiktok",
          "type": "url",
          "label": "Tiktok"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "メンバー",
      "settings": {
      },
      "blocks": [
        {
          "type": "image"
        }
      ]
    }
  ]
}
{% endschema %}

```

</details>

## theme.liquidにプラグインを追加する

`members.liquid`で、javascriptライブラリ`PhotoSwipe`と`Fontawesome(Brands)`を使用するので、`/layout/theme.liquid`内でインポートする。

## `PhotoSwipe`と`photoSwipeDynamicCaptionPlugin`をダウンロードする

[リンク](https://github.com/dimsemenov/PhotoSwipe "PhotoSwipe Download")の右上の「Code」から「Download ZIP」をクリックして、コードををダウンロードして解凍する。

解凍したフォルダ（luminous-main）から以下の JavaScript と CSS ファイルを`/assets`フォルダに移動。

- **/dist/umd/**
`photoswipe.umd.min.js`
`photoswipe-lightbox.umd.min.js`

- **/dist**
`photoswipe.css`

[リンク](https://github.com/dimsemenov/photoswipe-dynamic-caption-plugin "PhotoSwipeDynamicCaptionPlugin Download")の右上の「Code」から「Download ZIP」をクリックして、コードををダウンロードして解凍する。

解凍したフォルダから以下の JavaScript と CSS ファイルを`/assets`フォルダに移動。

- **/dist**
`photoswipe-dynamic-caption-plugin.umd.min.js`

- **/**
`photoswipe-dynamic-caption-plugin.css`

## Fontawesomeをダウンロード

[リンク](https://fontawesome.com/download "Fontwesome Download") の`Free For Web`からダウンロードして解凍する。

![FontAwesome6DownloadPage](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2788329/b5328652-1404-b38b-f5d1-7feb7382bf68.png)

解凍したフォルダ（fontawesome-x.x-web）の中の `/js` 内の以下の JavaScriptファイルを`/assets`フォルダに移動。

`fontawesome.min.js`
`brands.min.js`

## layout/theme.liquid に以下を追加

```liquid:theme.liquid
// {{ 'base.css' | asset_url | stylesheet_tag }} の直後


{{ 'photoswipe.umd.min.js' | asset_url | script_tag }}
{{ 'photoswipe-lightbox.umd.min.js' | asset_url | script_tag }}
{{ 'photoswipe-dynamic-caption-plugin.umd.min.js' | asset_url | script_tag }}
{{ 'fontawesome.min.js' | asset_url | script_tag }}
{{ 'brands.min.js' | asset_url | script_tag }}
```

## その他のテーマを調整する

トップページには、セクション(`featuredCollection`)を利用して「現在販売中のチケット」を表示しているが、コレクションが空の状態だと「商品名の例」として表示されてしまう。
そのため、`featured-collection.liquid`を編集してコレクションが空の状態の時はセクション自体が非表示となるように改める。

```liquid:featured-collection.liquid
{% if section.settings.collection.all_products_count != 0 %}
    <div class="color-{{ section.settings.color_scheme }} isolate gradient">
    ・
    ・
    ・
    </div>
{% endif %}
```
