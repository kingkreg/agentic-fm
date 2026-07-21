# FileMaker Layout Object Clipboard XML2

FileMaker layout objects copied from Layout Mode use the XML2 clipboard class and the `LayoutObjectList` wrapper. The inner syntax is the object dialect that FileMaker places on the clipboard:

```xml
<fmxmlsnippet type="LayoutObjectList">
  <Layout enclosingRectTop="114.0000000" enclosingRectLeft="0.0000000" enclosingRectBottom="1020.0000000" enclosingRectRight="812.0000000">
    <Object type="Text" key="1" LabelKey="0" flags="0" rotation="0">
      <Bounds top="117.0000000" left="20.0000000" bottom="138.0000000" right="127.0000000"/>
      <TextObj flags="0">...</TextObj>
    </Object>
  </Layout>
</fmxmlsnippet>
```

Do not generate the simplified `<LayoutObject type="...">` dialect for clipboard paste into FileMaker Layout Mode. That shape may look plausible, but FileMaker does not accept it as the native XML2 layout-object clipboard format in this project workflow.

## Required Structure

- Root element: `<fmxmlsnippet type="LayoutObjectList">`
- First child: `<Layout enclosingRectTop="..." enclosingRectLeft="..." enclosingRectBottom="..." enclosingRectRight="...">`
- Objects: `<Object type="Text|Field|Rect|Button|Portal|...">`
- Positioning: direct child `<Bounds top="..." left="..." bottom="..." right="..."/>`
- Text labels: `<TextObj>` with `CharacterStyleVector` and `ParagraphStyleVector` data nodes
- Fields: `<FieldObj>` with a visible `<Name>TO::Field</Name>` plus DDR field metadata
- Buttons: `<ButtonObj>` containing a script step, usually `<Step name="Perform Script">`

## Field Binding Pattern

Use the real XML2 field object pattern. The field reference is not a simplified `<FieldReference>` element.

```xml
<Object type="Field" key="2" LabelKey="1" flags="0" rotation="0">
  <Bounds top="114.0000000" left="138.0000000" bottom="145.0000000" right="391.0000000"/>
  <FieldObj numOfReps="1" flags="32" inputMode="0" keyboardType="1" displayType="0" quickFind="1" pictFormat="5">
    <Name>FuMail_QueueRun::AccountName</Name>
    ...
    <DDRInfo>
      <Field name="AccountName" id="15" repetition="1" maxRepetition="1" table="FuMail_QueueRun"/>
    </DDRInfo>
  </FieldObj>
</Object>
```

Both pieces matter:

- `<Name>` controls the displayed `TO::Field` binding.
- `<DDRInfo>/<Field>` carries the field name, field id, repetition metadata, and table occurrence name FileMaker expects.

## Button Pattern

Native layout buttons use an ordinary layout `<Object type="Button">` with a nested `ButtonObj`. Script wiring sits inside a script step, not a simplified action wrapper.

```xml
<Object type="Button" key="17001" LabelKey="0" flags="65544" rotation="0">
  <Bounds top="114.0000000" left="430.0000000" bottom="152.0000000" right="620.0000000"/>
  <TextObj flags="2">...</TextObj>
  <ButtonObj buttonFlags="0" iconSize="16" displayType="0">
    <Step enable="True" id="1" name="Perform Script">
      <Script id="1424" name="FuMail | Queue - Process Pending on Server"/>
    </Step>
  </ButtonObj>
</Object>
```

If a script parameter is needed, copy a real button with a parameter from FileMaker and mutate that known-good template. Do not invent the parameter XML from the script-step catalog; layout button XML2 has its own nested object shape.

## Practical Generation Strategy

The safest workflow is template mutation:

1. Ask the developer to copy a few existing layout objects from the target layout.
2. Read them with `python3 agent/scripts/clipboard.py read agent/sandbox/readback-layout.xml`.
3. Extract one real template object per required object type: `Text`, `Field`, `Rect`, `Button`, `Portal`.
4. Deep-copy each template and mutate only:
   - `key`
   - `Bounds`
   - text `Data` nodes
   - `FieldObj/Name`
   - `FieldObj/DDRInfo/Field` attributes
   - `ButtonObj/Step/Script` attributes
5. Write with `python3 agent/scripts/clipboard.py write generated-layout.xml`.

Validate generated layout XML with an XML parser. Do not use script-step validators for layout objects; they validate `FMObjectList` script-step snippets, not XML2 layout-object snippets.

## Theme Style Template Rule

When generating layout objects for paste into FileMaker, use copied theme-styled objects as templates wherever possible. A copied style palette is the preferred source because it carries the solution's named theme style references, button states, text runs, and object-specific XML2 structure.

For this workflow:

- Preserve named style references and `LocalCSS` metadata from the copied template object.
- Mutate only the object key, bounds, visible text, field binding metadata, and button script wiring unless the developer explicitly asks for a visual override.
- Keep inline `LocalCSS` overrides minimal. Layout snippets should inherit from FileMaker theme styles so later theme edits can update the appearance centrally.
- Avoid creating layout objects that arrive in FileMaker with the generic `default` style when a matching styled template exists.
- If no styled template exists for an object type, ask the developer to copy one representative object from the theme before producing reusable layout objects. Use a default/native fallback only for throwaway drafts or when the developer approves it.

## Portal Field Style Rule

Portal fields must distinguish display-only values from editable inputs.

For FuMail portal generation, use the copied portal style sample at `agent/sandbox/fumail-portal-field-styles-readback.xml` and the role map at `agent/sandbox/fumail-portal-field-style-map.json`:

- `display` fields use exported style `FM-82774B90-8462-4748-8C7A-7CABF19ABE7F`. Use this by default for non-editable portal fields such as dashboard lists, counts, statuses, timestamps, and template names.
- `edit` fields use exported style `FM-C4D78BE8-015A-4C8D-9301-E7FB4D216158`. Use this only where the user should type/select directly in the portal row.
- When mutating copied portal field templates, preserve the full copied `Styles` block. Change only the key, bounds, `FieldObj/Name`, and `FieldObj/DDRInfo/Field` metadata unless the developer asks for a visual override.

## Bounds Discipline For Slide Panels

When generating objects inside `SlidePanelObj`, child bounds are relative to the slide panel, not the page. Keep every direct child object's `Bounds right` at or below the slide panel width minus the intended padding. For the FuMail slide layouts currently using a `SlidePanel` right edge of `1108`, ordinary content should generally stop at `1064` or `1068`.

Use explicit `left` and `right` coordinates in helper functions for labeled fields. Do not pass a desired right edge into a parameter named `width`, and do not calculate a field's right edge as `left + right`. After generation, audit non-portal child objects and portal objects for `right > slide_panel_right`; this catches fields that would extend beyond the visible slider area in FileMaker Browse mode.

## References

| Name | Type | Local doc | Claris help |
|------|------|-----------|-------------|
| FileMaker clipboard classes | project doc | `agent/docs/CLIPBOARD.md` | n/a |
