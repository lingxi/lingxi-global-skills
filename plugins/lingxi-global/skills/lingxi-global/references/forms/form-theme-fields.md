# theme_config: page appearance

For general conventions, see [form-definition.md](form-definition.md). This section configures display styles; title, subtitle and body content stay at the top level of definition. Keep "the field can be saved" separate from "the client consumes it": for example, the current designer conversion consumes only some title styles, so do not promise that every style can be adjusted on every page just because the Schema declares it.

## Theme, container and header image

Field prefix: `definition.config.theme_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `logo` | object | Schema site logo config. |
| `logo.hidden` | scalar with Y/N semantics | 1 hide, 2 show; separate from footer_logo, which the frontend actually uses at the bottom. |
| `preset_theme_id` | scalar/null | Preset theme identifier; the frontend keeps it only while the header image still matches the theme, and a custom upload clears the old identifier. Candidates follow the current product. |
| `form_style` | object | Page style. |
| `form_style.selected` | `classic` / `modern` | Classic/light style; use the currently supported candidates. |
| `form_style.style` | open object list, max 50, candidates | Available style options. |
| `form_style.style[].code` | string, candidate | Style code. |
| `form_style.style[].title` | string, candidate | Style name. |
| `form_container` | object | Style of the form content area. |
| `form_container.background_color` | color string | Form area background color. |
| `form_container.border_color` | color string | Schema border color; the current theme conversion does not consume it, so verify the display effect in the actual client. |
| `form_container.border_width` | number/style value | Schema border width; the current conversion does not consume it. |
| `form_container.shadow` | scalar | Schema shadow setting, examples use Y/N; the current conversion does not consume it. |
| `header_image` | object | Form page header image; different from the portal image and the share image. |
| `header_image.hidden` | Y/N | 1 hide, 2 show. |
| `header_image.type` | `image`, frontend extension | Header image type saved by the frontend; the Schema accepts it through an open structure. |
| `header_image.image` | image object | Trusted image reference plus display size/position. |
| `header_image.image.width` | object | Declared header image width config; the current frontend mainly consumes height. |
| `header_image.image.height` | object | Header image height config. |
| `header_image.image.postion` | open object | **postion is the spelling the current frontend actually reads and writes**; do not correct it yourself. |
| `header_image.image.postion.x` | percentage string | Horizontal image position, such as `50%`. |
| `header_image.image.postion.y` | percentage string | Vertical image position, such as `50%`. |
| `header_image.image.position` | open object, compatibility difference | Backend edit defaults may return position, and the Schema also accepts it; the current frontend mainly reads and writes postion, so the display effect of this alias is not guaranteed. |
| `header_image.image.position.x` | percentage string | Horizontal coordinate of the backend position structure. |
| `header_image.image.position.y` | percentage string | Vertical coordinate of the backend position structure. |
| `form_title` | object | Styles for the three page text blocks. |
| `form_title.title` | shared text style | Main title style. |
| `form_title.subtitle` | shared text style | Subtitle style. |
| `form_title.description` | shared text style | Detail body style. |
| `wallpaper` | object | Background around the page. |
| `wallpaper.background_color` | object | Background color settings. |
| `wallpaper.background_color.enabled` | Y/N | Whether to use a solid background; mutually exclusive with the background image. |
| `wallpaper.background_color.color` | color string | Page background color. |
| `wallpaper.background_image` | image object | Page background image. |
| `wallpaper.background_image.enabled` | Y/N | Whether to use an image background. |
| `wallpaper.background_image.file_width` | number, read back | Original width of the background image. |
| `wallpaper.background_image.file_height` | number, read back | Original height of the background image. |
| `submit_button` | shared button style | Submit button appearance. |
| `add_to_cart_button` | shared button style | Appearance of the button used with payment questions/the cart flow; whether it shows also depends on the flow. |
| `footer_logo` | object, frontend extension | Footer logo config the frontend actually uses; not explicitly enumerated in the Schema, saved through the root open structure. |
| `footer_logo.hidden` | Y/N | 1 hide the footer logo, 2 show. |

Field prefixes: `definition.config.theme_config.header_image.image.width`, `definition.config.theme_config.header_image.image.height`

| Field | Type/limit | Purpose |
| --- | --- | --- |
| `default` | number | The size value actually in use, such as height.default=280, in frontend pixel semantics. |
| `value` | scalar list, max 100, candidates | List of selectable sizes, such as 120/176/280; it does not set several sizes at once. |

Field prefixes: `definition.config.theme_config.header_image.image`, `definition.config.theme_config.wallpaper.background_image`

| Field | Type | Purpose |
| --- | --- | --- |
| `upload_id` | UUID | Reference to a real new upload, validated separately by the Request; an arbitrary URL cannot replace it. |
| `id` | real file reference | Existing image identifier; ownership is still validated. |
| `path` | string, read back | File path for display. |
| `url` | string, read back | Image access URL. |
| `original_name` | string, read back | Original file name. |

For updates, use `changes.config.theme_config.header_image`. Inside the image object, keys merge recursively: passing only `image.height.default` keeps the existing `height.value`, the original image reference and the position; `postion.x` does not overwrite an omitted `postion.y`. Lists (including the empty list `[]`) are replaced as a whole; old trailing items are not kept by index. `image:null` explicitly clears the image; omitting the image or passing an empty `upload_id` keeps the original file; to replace the image, first complete the real byte upload, then submit the new `image.upload_id`. This merge rule is specific to the header image; it does not mean all theme config supports recursive merging.

## Text and button styles

Field prefixes: `definition.config.theme_config.form_title.title`, `definition.config.theme_config.form_title.subtitle`, `definition.config.theme_config.form_title.description`

| Field | Type/values | Purpose/boundary |
| --- | --- | --- |
| `hidden` | Y/N | 1 hide this text, 2 show. |
| `font_size` | CSS size string | Font size, such as `24px`; the current conversion mainly handles visibility and alignment, so verify other styles by rendering. |
| `font_weight` | CSS font weight | Such as `bold/400`; do not promise display on every client based on the Schema alone. |
| `color` | color string | Text color; display consumption as above. |
| `text_align` | `left/center/right` | Text alignment; the current description conversion always uses left. |

Field prefixes: `definition.config.theme_config.submit_button`, `definition.config.theme_config.add_to_cart_button`

| Field | Type/values | Purpose/boundary |
| --- | --- | --- |
| `enabled` | Y/N | In submit_button it only keeps the translation item structure consistent and **is not the button visibility switch**; the cart button is additionally constrained by the payment business flow. |
| `hidden` | Y/N | The actual visibility flag, 1 hide, 2 show. |
| `text` | string | Button text; can enter the translation catalog. |
| `font_size` | CSS size string | Button font size, such as `16px`. |
| `font_weight` | CSS font weight | Schema button font weight; the current submit conversion does not consume this field. |
| `color` | color string | Schema text color; the current submit conversion does not consume this field. |
| `background_color` | color string | Button background color. |

Presets, the current Schema example and the edit-default header image height are not fully identical. Use the candidates/read back from this session; do not treat examples as the actual defaults. For the header image upload and save flow, see [header-image.md](header-image.md).

