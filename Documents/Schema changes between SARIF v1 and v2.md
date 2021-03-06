R# Schema changes between SARIF v1 and v2

This document summarizes the changes in the SARIF JSON schema between v1 (the version
defined by the initial working draft and implemented by many Microsoft tools such as its C++, C#, and VB compilers) and v2 (the first public version released by the
OASIS SARIF TC).

The changes are presented in order of approval.
In some cases, a later change overrides an earlier change.
These overrides are noted on the earlier change. 

## Changes approved and merged into the provisional draft

- Remove the following properties:

    - `annotatedCodeLocation.id`
    - `annotatedCodeLocation.essential`.

    These properties, defined in the JSON schema but marked "OBSOLETE", were holdovers from versions of the SARIF format prior to the first working draft.

- [Issue 25](https://github.com/oasis-tcs/sarif-spec/issues/25): `result.message` is not
    required.

    Issue 25 clarifies that one of `result.message` or `result.templatedMessage` is required,
    which means that the schema can't mark the `message` property as `required`.

    2018/03/20: OBSOLETE. There is no longer a `result.templatedMessage` property. The `result.message` property is now a `message` object, whose schema is defined below in #84 (localization)

- [Issue #27](https://github.com/oasis-tcs/sarif-spec/issues/27): "Add a help property to rule"

    In the `rule` object:

    - Add the property `help` of type `string`, optional.

    2018/03/20: As of #84, `rule.help` is now a `message` object.

- [Issue #33](https://github.com/oasis-tcs/sarif-spec/issues/33): "Should we allow formatting in messages?"

    Add the following optional properties, containing rich text messages:

    - `annotatedCodeLocation.richMessage`
    - `annotation.richMessage`
    - `codeFlow.richMessage`
    - `fix.richDescription`
    - `result.richMessage`
    - `rule.richDescription`
    - `rule.richHelp`
    - `rule.richMessageTemplates`
    - `stack.richMessage`
    - `stackFrame.richMessage`

    Due to confusion about the term "formatted message"
    (some people thought it meant "messages containing Markdown",
    although the spec originally used it to mean "messages with `{n}` replacement sequences"),
    rename the following objects:

    - `formattedMessage` &rarr; `templatedMessage`
    
    For the same reason, rename the following properties:

    - `formattedMessage.formatId` &rarr; `templatedMessage.templateId`
    - `result.formattedRuleMessage` &rarr; `result.templatedMessage`
    - `rule.messageFormats` &rarr; `rule.messageTemplates`
    
	Add the following optional property with the specified default value:

    - `run.richMessageMimeType`: `"text/markdown;variant=GFM"`.

    2018/03/20: OBSOLETE *except* for `run.richMessageMimeType`. These properties are now subsumed into the corresponding `message` properties, which are now `message` objects.

- [Issue #61](https://github.com/oasis-tcs/sarif-spec/issues/61): "Provide a format for links embedded in our plain text messages"

    In the `physicalLocation` object:

    - Add the property `id` of type `integer`, optional.

- [Issue #69](https://github.com/oasis-tcs/sarif-spec/issues/69): "Provide a physicalLocation on a stack frame"

    In the `stackFrame object`:

    - Remove the properties `uri`, `uriBaseId`, `line`, and `column`.
    - Add the property `physicalLocation` of type `physicalLocation`, optional.

    NOTE: As a result of #130, this `physicalLocation` will be absorbed into the `location` property of type `location`.

- [Issue #72](https://github.com/oasis-tcs/sarif-spec/issues/72): "tool.language property needs a default value"

    Specify a default value for the following optional property, which subsumes the deleted properties:

    - `tool.language`: `"en-US"`

- [Issue #66](https://github.com/oasis-tcs/sarif-spec/issues/66): "Enable traceability from converted SARIF file to original analysis tool log file"

    Define the `conversion` object with the following properties:

    - `tool` of type `tool`, required
    - `invocation` of type `invocation`, optional
    - `analysisToolLogFileUri` of type `string`, containing a valid URI, optional
    - `analysisToolLogFileUriBaseId` of type `string`, containing a URI base id, optional

    Define the `analysisToolLogFileContents` object with the following properties:

    - `region` of type `region`, optional
    - `snippet` of type `string`, optional
    - `analysisToolLogFileUri` of type `string`, containing a valid URI, optional
    - `analysisToolLogFileUriBaseId` of type `string`, containing a URI base id, optional

    NOTE: As a result of #130, this object will disappear.

    In the `run` object:

    - Add the property `conversion` of type `conversion`, optional.

    In the `result` object:

    - Add the property `conversionProvenance` of type `analysisToolLogFileContents[]`, unique, optional.

    NOTE: As a result of #130, the type of this property will change to `physicalLocation[]`.

- [Issue #81](https://github.com/oasis-tcs/sarif-spec/issues/81): "Add 'open' as a result level"

    In the `result` object:

    - Add an additional enumerated value `"open"` to the `level` property.

- [Issue #82](https://github.com/oasis-tcs/sarif-spec/issues/82): "Add instance id to result object"

    In the `result` object:

    - Add the property `id` of type `string`, optional

    NOTE: As a result of #159, the property name is `instanceGuid`.

- [Issue #90](https://github.com/oasis-tcs/sarif-spec/issues/90): "Introduce fileLocation object"

    Define the `fileLocation` object with the following properties:

    - `uri` of type `string`, required
    - `uriBaseId` of type `string`, optional

    In the `conversion` object:

    - Remove the properties `analysisToolLogFileUri` and `analysisToolLogFileUriBaseId`.
    - Add the property `analysisToolLogFileLocation` of type `fileLocation`, optional.

    In the `file`object:

    - Remove the properties `uri` and `uriBaseId`.
    - Add the property `fileLocation` of type `fileLocation`, optional.

    In the `analysisToolLogFileContents` object:

    - Remove the properties `analysisToolLogFileUri` and `analysisToolLogFileUriBaseId`.
    - Add the property `analysisToolLogFileLocation` of type `fileLocation`, optional.

    NOTE: As a result of #130, this is moot. There is no longer an `analysisToolLogFileContents` object.

    In the `physicalLocation` object:

    - Remove the properties `uri` and `uriBaseId`.
    - Add the property `fileLocation` of type `fileLocation`, optional.

    In the `fileChange` object:

    - Remove the properties `uri` and `uriBaseId`.
    - Add the property `fileLocation` of type `fileLocation`, optional.

    In the `invocation` object:

    - Change the type of the `responseFiles` property from `object` with `string`-valued properties to `fileLocation[]`.

    NOTE: I originally missed the change to `invocation.responseFiles`. I incorporated that change into the change draft for #76/#97.

- [Issue #83](https://github.com/oasis-tcs/sarif-spec/issues/83): "Consider adding attachments property"

    Define the `attachment` object with the following properties:

    - `description` of type `string`, optional.
    - `fileLocation` of type `fileLocation`, required.

    In the `invocation` object:

    - Add the property `attachments` of type `attachment[]`, optional.

    In the `result` object:

    - Add the property `attachments` of type `attachment[]`, optional.

- [Issue #94](https://github.com/oasis-tcs/sarif-spec/issues/94): "Add an invocation.arguments property"

    In the `invocation` object:

    - Add the property `arguments` of type `string[]`, optional.

- [Issue #91](https://github.com/oasis-tcs/sarif-spec/issues/91): "Represent original values for uriBaseId properties"

    In the `run` object:

    - Add the property `originalUriBaseIds`, of type `object` with property values of type `string`.

- [Issue #92](https://github.com/oasis-tcs/sarif-spec/issues/92): "Add stdin/stdout/stderr on invocation"

    In the `invocation` object:

    - Add the properties `stdin`, `stdout`, and `stderr` of type `string`, optional.

    NOTE: As a result of #133, these are `fileLocation` objects instead of `string`s.

- [Issue #10](https://github.com/oasis-tcs/sarif-spec/issues/10): "Do we want an array of fingerprint contributions on result?"

    In the `result` object:

    - Rename the `toolFingerprintContribution` property to `toolFingerprintContributions`.
    - Changed the type of the renamed `toolFingerprintContributions` property from `string` to `object` with property values of type `string`.

    NOTE: As a result of #147, `toolFingerprintContributions` is named `partialFingerprints`.

- [Issue #15](https://github.com/oasis-tcs/sarif-spec/issues/15): "Document how converters should provide notifications"

    In the `conversion` property:

    - Add the `notifications` property of type `notification[]`

    2018/03/20: OBSOLETE. `conversion` has an `invocation` property, and `invocation` now has `toolNotifications` and `configurationNotifications` properties. Filed [Issue #132](https://github.com/oasis-tcs/sarif-spec/issues/132): "conversion.notifications is superfluous" to remove this property. Don't add it to the schema.

- [Issue #84](https://github.com/oasis-tcs/sarif-spec/issues/84): "Enable localization for all message strings"

    Define a `message` object with the following properties:

    - `text` of type `string`, optional.
    - `richText` of type `string`, optional.
    - `messageId` of type `string`, optional.
    - `richMessageId` of type `string`, optional.
    - `arguments` of type `string[]`, optional.

    Define a `resources` object with the following properties:

    - `messageStrings` of type `object` with property values of type `string`.
    - `rules` of type `object` with property values of type `rule`.

    Remove the entire `templatedMessage` object.

    In the `run` object:

    - `results` is now required (UNLESS we decide to validate resource files against the same schema as log files).
    - Remove the `rules` property.
    - Add the property `resources` of type `resources`.

    In the `invocation` object:

    - The `attachments` property is now a unique array with at least 1 item.

    In the `attachment` object:

    - Change the type of the `description` property from `string` to `message`.

    In the `result` object:

    - Change the type of the `message` property from `string` to `message`.
    - Add the property `ruleMessageId` of type `string`.
    - Remove the `richMessage` property.
    - Remove the `templatedMessage` property.
    - The `attachments` property is now a unique array with at least 1 item.

    In the `codeFlow` object:

    - Change the type of the `message` property from `string` to `message`.
    - Remove the `richMessage` property.

    In the `stack` object:

    - Change the type of the `message` property from `string` to `message`.
    - Remove the `richMessage` property.

    In the `stackFrame` object:

    - Change the type of the `message` property from `string` to `message`.
    - Remove the `richMessage` property.

    In the `annotatedCodeLocation` object:

    - Change the type of the `message` property from `string` to `message`.
    - Remove the `richMessage` property.

    In the `annotation` object:

    - Change the type of the `message` property from `string` to `message`.
    - Remove the `richMessage` property.

    In the `rule` object:

    - Change the type of the `name` property from `string` to `message`.
    - Change the type of the `shortDescription` property from `string` to `message`.
    - Change the type of the `fullDescription` property from `string` to `message`.
    - Remove the `richDescription` property.
    - Rename the `messageTemplates` property to `messageStrings`.
    - Rename the `richMessageTemplates` property to `richMessageStrings`.
    - Change the type of the `help` property from `string` to `message`.
    - Remove the `richHelp` property.

    In the `fix` object:

    - Change the type of the `description` property from `string` to `message`.
    - Remove the `richDescription` property.

    In the `notification` object:

    - Change the type of the `message` property from `string` to `message`.

- [Issue #29](https://github.com/oasis-tcs/sarif-spec/issues/29): "Introduce object-valued rule.configuration"

    Define a `ruleConfiguration` object with the following properties:

    - `enabled` of type `boolean`, optional, default = `true`.
    - `defaultLevel` of type `string`, enumerated values `warning`, `error`, `note`, or `open`, default `warning`.
    - `parameters`, a property bag.

    NOTE: #105 removes the default.

    In the `rule` object:

    - Remove the `defaultLevel` property.
    - Add the property `configuration` of type `ruleConfiguration`.

- [Issue #110](https://github.com/oasis-tcs/sarif-spec/issues/110): "Specify how to treat a file that contains interleaved stdout/stderr"

    In the `invocation` object:

    - Add the property `stdoutStderr` of type `string`.

    NOTE: Because of #133, it is now of type `fileLocation`.

- [Issue #102](https://github.com/oasis-tcs/sarif-spec/issues/102): "run.invocation should be an array of invocation objects"

    In the `run` object:

    - Rename the `invocation` property to `invocations`.
    - Change the type of the renamed `invocations` property to from `invocation` to `invocation[]`

    NOTE: The name change was not in the change draft. I made an editorial change for that after the fact.

- [Issue #97](https://github.com/oasis-tcs/sarif-spec/issues/97): "file object's contents property"

    Define a `fileContent` object with the following properties:

    - `text` of type `string`, optional.
    - `binary` of type `string`, optional.

    In the `file` object:

    - Change the type of the `contents` property from `string` to `fileContent`.

    In the `result` object:

    - Change the type of the `snippet` property from `string` to `fileContent`.

    In the `analysisToolLogFileContents` object:

    - Change the type of the `snippet` property from `string` to `fileContent`.

    OBSOLETE: Because of #130.

    In the `annotatedCodeLocation` object:

    - Change the type of the `snippet` property from `string` to `fileContent`.

    In the `replacement` object:

    - Remove the `offset` property
    - Change the name of the `deletedLength` property to `deletedRegion`.
    - Change the type of the renamed `deletedRegion` property from `integer` to `region`.
    - Change the name of the `insertedBytes` property to `insertedContent`.
    - Change the type of the renamed `insertedContent` property from `string` to `fileContent`.

- [Issue #115](https://github.com/oasis-tcs/sarif-spec/issues/115): "invocation object should record process outcome"

    In the `invocation` object:

    - Add the property `exitCode` of type `integer`.
    - Add the property `exitCodeDescription` of type `string`.
    - Add the property `exitSignalName` of type `string`.
    - Add the property `exitSignalNumber` of type `integer`.
    - Add the property `processStartFailureMessage` of type `string`.
    - Add the property `processExitedSuccessfully` of type `boolean`.

    NOTE: The change draft has `processSuccessful` for that last property. The final agreed-upon name is `toolExecutionSuccessful`.

- Editorial discretion: `toolNotifications` and `configurationNotifications` are now on `invocation` instead of `run`.

- [Issue #75](https://github.com/oasis-tcs/sarif-spec/issues/75): "Ensure spec properly accounts for tools that emit line #'s only for code locations"

    In the `run` object:

    - Remove `"uniqueItems"` from the definition of the `results` property.

- [Issue #80](https://github.com/oasis-tcs/sarif-spec/issues/80): "Code flow enhancements"

    Rename the existing `codeFlow` object to `threadFlow`.

    In the renamed `threadFlow` object:

    - Add a property `id` of type `string`, optional.

    Define a new `codeFlow` object with the following properties:

    - `message` of type `message`, optional.
    - `threadFlows` of type `threadFlow[]`, required, unique, non-empty.
    - `properties` of type `propertyBag`, optional.

    In the `annotatedCodeLocation` object:

    - Remove the `kind` property.
    - Remove the `target` property.
    - Remove the `values` property.
    - Remove the `taintKind` property.
    - Remove the `threadId` property.
    - Add a property `nestingLevel` of type `integer`, optional.
    - Add a property `executionOrder` of type `integer`, optional.

    NOTE: Because of #161, `kind` is back, but it means something different.

- [Issue #86](https://github.com/oasis-tcs/sarif-spec/issues/86): "Add path normalization guidance for URLs"

    In the `rule` object:

    - Rename the `helpUri` property to `helpLocation`.
    - Change the type of the renamed `helpLocation` property from `string` to `fileLocation`.

    OBSOLETE: Because of #175.

- [Issue #95](https://github.com/oasis-tcs/sarif-spec/issues/95): "invocation.fileName -> invocation.executableLocation"

    In the `invocation` object:

    - Rename the `fileName` property to `executableLocation`.
    - Change the type of the renamed `executableLocation` property from `string` to `fileLocation`.

- [Issue #96](https://github.com/oasis-tcs/sarif-spec/issues/96): "Add redactionToken property to run object"

    In the `run` object:

    - Add the property `redactionToken` of type `string`.

- [Issue #133](https://github.com/oasis-tcs/sarif-spec/issues/133): "Make stdin/stdout/stderr/stdoutStderr fileLocation instead of physicalLocation"

    In the `invocation` object:

    - Change the types of the `stdin`, `stdout`, `stderr`, and `stdoutStderr` properties from `physicalLocation` to `fileLocation`.

- [Issue #105](https://github.com/oasis-tcs/sarif-spec/issues/105): "Remove `default` from `result.level` property in schema"

    - Remove the `"default"` value from the `level` property.
    - Improve the description of the `level` property.

    NOTE: There is no spec change here. The spec language is correct. The change is entirely in the JSON schema.

- [Issue #130](https://github.com/oasis-tcs/sarif-spec/issues/130): "Fix the location object"

    Rename the type `annotatedCodeLocation` to `codeFlowLocation`.

    NOTE: As of #187, it is `threadFlowLocation`.

    Remove the `analysisToolLogFileContents` object

    In the `location` object:

    - Remove the `analysisTarget` property.
    - Rename the `resultFile` property to `physicalLocation`.
    - Add a property `message` of type `message`, optional.
    - Add a property `annotations` of type `annotation[]`, optional.

    NOTE: As of #155, `annotations` is of type `region[]`.

    In the `result` object:

    - Add a property `analysisTarget` of type `fileLocation`, optional.
    - Change the type of property `relatedLocations` from `annotatedCodeLocation[]` to `location[]`.
    - Change the type of property `conversionProvenance` from `analysisToolLogFileContents[]` to `physicalLocation[]`.
    - Remove the `snippet` property.

    In the `threadFlow` object:

    - Change the type of property `locations` from `annotatedCodeLocation[]` to `codeFlowLocation[]`.

    In the `physicalLocation` object:

    - Add a property `contextRegion` of type `region`, optional.

    In the `region` object:

    - Add a property `snippet` of type `fileContent`, optional.

    In the (renamed) `codeFlowLocation` object:

    - Remove the `physicalLocation` property.
    - Remove the `fullyQualifiedLogicalName` property.
    - Remove the `logicalLocationKey` property.
    - Remove the `message` property.
    - Remove the `annotations` property.
    - Remove the `snippet` property. (It is now at `codeFlowLocation.location.physicalLocation.region.snippet`.)
    - Add a `location` property of type `location`, required.

    NOTE: `location` is no longer required (some `codeFlowLocation`s just carry a `message`).

    In the `stackFrame` object:

    - Remove the `physicalLocation` property.
    - Remove the `fullyQualifiedLogicalName` property.
    - Remove the `logicalLocationKey` property.
    - Remove the `message` property.
    - Add a `location` property of type `location`.

- [Issue #46](https://github.com/oasis-tcs/sarif-spec/issues/46): "Provide support for graphs and graph traversals."

    Define the `graph` object with the following properties:

    - `id` of type `string`, required.
    - `description` of type `message`, optional.
    - `nodes` of type `node[]`, required, unique.
    - `edges` of type `edge[]`, required, unique.
    - `properties` of type `propertyBag`, optional.

    Define the `node` object with the following properties:

    - `id` of type `string`, required.
    - `label` of type `message`, optional.
    - `location` of type `location`, required.
    - `properties` of type `propertyBag`, optional.

    Define the `edge` object with the following properties:

    - `id` of type `string`, required.
    - `label` of type `message`, optional.
    - `sourceNodeId` of type `string`, required.
    - `targetNodeId` of type `string`, required.
    - `properties` of type `propertyBag`, optional.

    Define the `graphTraversal` object with the following properties:

    - `graphId` of type `string`, required.
    - `description` of type `message`, optional.
    - `initialState` of type `object`, optional, properties of type `string`.
    - `edgeTraversals` of type `edgeTraversal[]`, required.
    - `properties` of type `propertyBag`, optional.

    Define the `edgeTraversal` object with the following properties:

    - `edgeId` of type `string`, required.
    - `message` of type `message`, optional.
    - `finalState` of type `object`, optional, properties of type `string`.
    - `properties` of type `propertyBag`, optional.

    In the `run` object:

    - Add a `graphs` property of type `graph[]`, optional.

    In the `result` object:

    - Add a `graphs` property of type `graph[]`, optional.
    - Add a `graphTraversals` property of type `graphTraversal[]`, optional.

- [Issue #98](https://github.com/oasis-tcs/sarif-spec/issues/98): "Add encoding property to file object"

    In the `run` object:

    - Add the property `defaultFileEncoding` of type `string`.

    In the `file` object:

    - Add the property `encoding` of type `string`.

- [Issue #107](https://github.com/oasis-tcs/sarif-spec/issues/107): "Settle on a small set of hash functions"

    In the `hash` object:

    - Change the type of property `algorithm` from `enum` to `string`.

    Also: Wherever the word "algorithm" occurs in a **comment**, change it to "hash function".

- [Issue #108](https://github.com/oasis-tcs/sarif-spec/issues/108): "Represent VCS properties"

    Define the `versionControlDetails` object with the following properties:

    - `uri` of type `string`, containing a valid URI, required.
    - `revisionId` of type `string`, optional.
    - `branch` of type `string`, optional.
    - `tag` of type `string`, optional.
    - `timestamp` of type `string` in `date-time` format, optional.
    - `properties` of type `propertyBag`, optional.

    In the `run` object:

    - Add a `versionControlProvenance` property of type `versionControlDetails[]`, unique, minItems = 1, optional.

- [Issue #120](https://github.com/oasis-tcs/sarif-spec/issues/120): "Identify files that were scanned"

    In the `file` object:

    - Add the property `roles` of type `string[]`, with each array element having one of the enumerated values:
        - `"analysisTarget"`
        - `"attachment"`
        - `"responseFile"`
        - `"resultFile"`
        - `"screenshot"`
        - `"standardStream"`
        - `"traceFile"`

    NOTE: As of #137, `"screenshot"` is gone.

- [Issue #126](https://github.com/oasis-tcs/sarif-spec/issues/126): "Add result.fingerprints array"

    In the `result` object:

  - Add a `fingerprints` property of type `object` with `string`-valued properties.

- [Issue #147](https://github.com/oasis-tcs/sarif-spec/issues/147): "Rename suggestion: toolFingerprintContributions -> partialFingerprints, computedFingerprints -> fingerprints"

    In the `result` object:

    - Rename the `toolFingerprintContributions` property to `partialFingerprints`.

    NOTE: We added `fingerprints` directly in #126, so there's nothing to do for "`computedFingerprints`" (it never existed by that name).

- [Issue #134](https://github.com/oasis-tcs/sarif-spec/issues/134): "conversion.analysisToolLogFileLocation should be an array"

    In the `conversion` object:

    - Rename the `analysisToolLogFileLocation` property to `analysisToolLogFiles`
    - Change the type of the renamed property `analysisToolLogFiles` from `fileLocation` to `fileLocation[]`.

- [Issue #137](https://github.com/oasis-tcs/sarif-spec/issues/137): "Support annotating image attachments"

    Define the `rectangle` object with the following properties:

    - `top` of type `number`
    - `left` of type `number`
    - `bottom` of type `number`
    - `right` of type `number`
    - `message` of type `message

    In the `region` object:

    - Add a `message` property of type `message`.

    In the `attachment` object:

    - Add a `regions` property of type `region[]`, minItems 1, unique.
    - Add a `rectangles` property of type `rectangle[]`, minItems 1, unique.

    In the `file` object:

    - Remove the enumerated value `"screenshot"` from the `roles` property.

- [Issue #139](https://github.com/oasis-tcs/sarif-spec/issues/139): "Don't require codeFlowLocation.location"

    In the `codeFlowLocation` object:

    - Remove `location` from the list of required properties.

    NOTE: In the schema file, `codeFlowLocation.location` was never mentioned as required. This was just as spec change.

- [Issue #145](https://github.com/oasis-tcs/sarif-spec/issues/145): "For symmetry, define a logicalLocation object"

    In the `location` object:

    - Remove the `logicalLocationKey` property.
    - Remove the `decoratedName` property.

    In the `logicalLocation` object:

    - Add a property `fullyQualifiedName` of type `string`.
    - Add a property `decoratedName` of type `string`.

    In the `result` object:

    - Remove the `ruleKey` property.

    In the `notification` object:

    - Remove the `ruleKey` property.

- [Issue #155](https://github.com/oasis-tcs/sarif-spec/issues/155): "Remove annotations object; use regions instead"

    Remove the `annotation` object.

    In the `location` object:

   - Change the type of `annotations` from `annotation[]` to `region[]`.

    In the `physicalLocation` object:

   - Make `fileLocation` required.

- [Issue #138](https://github.com/oasis-tcs/sarif-spec/issues/138): "Consider a download/install uri for the tool"

    In the `tool` object:

    - Add a `downloadUri` property of type `string` in `uri` format, optional.

- [Issue #141](https://github.com/oasis-tcs/sarif-spec/issues/141): "Consider adding timestamp to file object"

    In the `file` object:

    - Add a `lastModifiedTime` property of type `string` in `date-time` format, optional. 

- [Issue #143](https://github.com/oasis-tcs/sarif-spec/issues/143): "Add 'returnType', 'parameter' and 'variable' to logical location kind enum"

    In the `logicalLocation` object:

    - Add `"returnType"`, `"parameter"`, and `"variable"` to the `"description"` of the `kind` property.

    NOTE: `logicalLocation.kind` is not an `enum`.
    The spec recommends certain values, but any value is permitted.
    So the only change to the schema is this comment change.

- [Issue #157](https://github.com/oasis-tcs/sarif-spec/issues/157): "Clarify requirements on tool.semanticVersion"

    In the `tool` object:

    - `semanticVersion` is no longer `required`.

    NOTE: There is no actual change to the schema here, because it had failed to declare `semanticVersion` as `required` in the first place.

- [Issue #159](https://github.com/oasis-tcs/sarif-spec/issues/159): "Id property renames"

    In the `run` object:

    - Rename the `id` property to `instanceGuid`.
    - Rename the `stableId` property to `logicalId`.
    - Rename the `baselineId` property to `baselineInstanceGuid`.
    - Rename the `automationId` property to `automationLogicalId`.

    In the `result` object:

    - Rename the `id` property to `instanceGuid`.

- [Issue #160](https://github.com/oasis-tcs/sarif-spec/issues/160): "Roles for edited files"

    In the `file` object:

    - Add the following enumerated values to the `roles` property:
        - `"unmodifiedFile"`
        - `"modifiedFile"`
        - `"addedFile"`
        - `"deletedFile"`
        - `"renamedFile"`
        - `"uncontrolledFile"`

- [Issue #161](https://github.com/oasis-tcs/sarif-spec/issues/161): "Expand code flows to represent dynamic execution events"

    In the `codeFlowLocation` object:

    - Add a `stack` property of type `stack`.
    - Add a `kind` property of type `string`.

- [Issue #163](https://github.com/oasis-tcs/sarif-spec/issues/163): "Add result.workItemLocation"

    In the `result` object:

    - Add a `workItemLocation` property of type `fileLocation`.

- [Issue #165](https://github.com/oasis-tcs/sarif-spec/issues/165): "Add run.description"

    In the `run` object:

    - Add a `description` property of type `message`.

- [Issue #176](https://github.com/oasis-tcs/sarif-spec/issues/176): "fileLocation uri property should be a URI reference"

    In the `fileLocation` object:

    - Change the `uri` property from `"format": "uri"` to `"format": "uri-reference"`.

- [Issue #93](https://github.com/oasis-tcs/sarif-spec/issues/93): "Problems with regions"

    In the `region` object:

    - Remove the `offset` property.
    - Remove the `length` property.
    - Add a `charOffset` property of type `integer`, optional.
    - Add a `charLength` property of type `integer`, optional.
    - Add a `byteOffset` property of type `integer`, optional.
    - Add a `byteLength` property of type `integer`, optional.

- [Issue #149](https://github.com/oasis-tcs/sarif-spec/issues/149): "Support nested graphs"

    In the `node` object:

    - Add a `children` property of type `node[]`, optional, unique.

    In the `graphTraversal` object"

    - Remove the `id` property.

    In the `edgeTraversal` object:

    - Remove the `nestedGraphTraversalId` property.
    - Add a `stepOverEdgeCount` property of type `integer`, optional.

- [Issue #158](https://github.com/oasis-tcs/sarif-spec/issues/158): "Introduce result.correlationId and clarify purpose of result.fingerprints array"

    In the `result` object:

    - Add a `correlationGuid` property of type `string`, optional.

- [Issue #187](https://github.com/oasis-tcs/sarif-spec/issues/187): "codeFlowLocation => threadFlowLocation"

    Rename the `codeFlowLocation` object to `threadFlowLocation`.

- [Issue #172](https://github.com/oasis-tcs/sarif-spec/issues/172): "Dynamic code flows emit timestamps"

    In the `threadFlowLocation` object:

    - Add a `timestamp` property of time `string` in `date-time` format, optional.

- [Issue #175](https://github.com/oasis-tcs/sarif-spec/issues/175): "Decide on policy for fileLocation vs. URI"

    In the `result` object:

    - Rename the `workItemLocation` property to `workItemUri`, and change its type from `fileLocation` to `string` with `uri` format.

    In the `rule` object:

    - Rename the `helpLocation` property to `helpUri`, and change its type from `fileLocation` to `string` with `uri` format.

- [Issue #178](https://github.com/oasis-tcs/sarif-spec/issues/178): "Support a character or column interpretation property"

    In the `run` object:

    - Add a `columnKind` property of type `string` with enumerated values `utf16CodeUnits` and `unicodeCodePoints`, required.

    NOTE: As of #191, this property is optional.

- [Issue #189](https://github.com/oasis-tcs/sarif-spec/issues/189): "Make result.workItemUris an array"

    In the `result` object:

    - Rename the `workItemUri` property to `workItemUris`.
    - Change its type from `string` to `string[]`, unique, minItems = 1.

- [Issue #191](https://github.com/oasis-tcs/sarif-spec/issues/191): "run.columnKind is optional"

    In the `run` object:

    - Make the `columnKind` property optional.

---
End of changes for CSD.1
---
Start of changes for CSD.2
---

- [Issue #216](https://github.com/oasis-tcs/sarif-spec/issues/216): "Remove ruleMessageId and update message lookup semantics."

    In the `result` object:

    - Remove the property `ruleMessageId`.

- [Issue #220](https://github.com/oasis-tcs/sarif-spec/issues/220): "Add a correlationGuid property to run"

    In the `run` object:

    - Add a `correlationGuid` property of type `string`, optional.

- [Issue #222](https://github.com/oasis-tcs/sarif-spec/issues/222): "Make invocation.workingDirectory a fileLocation object"

    In the `invocation` object:

    - Change the type of the `workingDirectory` property from `string` to `fileLocation`.

- [Issue #174](https://github.com/oasis-tcs/sarif-spec/issues/174): "Result mgmt. systems merge SARIF files. Can we provide a count of occurrences?"

    In the `result` object:

    - Add an `occurrenceCount` property of type `integer`, minVal = 1, optional.

- [Issue #233](https://github.com/oasis-tcs/sarif-spec/issues/233): "Consider not requiring rule.id"

    In the `rule` object:

    - Make the `id` property optional.

- [Issue #237](https://github.com/oasis-tcs/sarif-spec/issues/237): "run.graphs and result.graphs should be dictionaries, not arrays."

    In the `run` object:

    - Change the type of the `graphs` property from `graph[]` to `object` with `graph`-valued properties.

    In the `result` object:

    - Change the  type of the `graphs` property from `graph[]` to `object` with `graph`-valued properties.

- [Issue #234](https://github.com/oasis-tcs/sarif-spec/issues/234): "Consider making originalUriBaseIds a dictionary of file location objects"

    In the `run` object:

    - Change the type of the `originalUriBaseIds` property from `object` with `string`-valued properties to `object` with `fileLocation`-valued properties.

- [Issue #235](https://github.com/oasis-tcs/sarif-spec/issues/235): "Wrap externalized files with contextual information"

    Define an `externalFile` object with the following properties:

    - `fileLocation` of type `fileLocation`, required.
    - `instanceGuid` of type `string`, required.

    In the `run` object:

    - Define an `externalFiles` property of type `object` with `externalFile`-valued properties, optional.

- [Issue #238](https://github.com/oasis-tcs/sarif-spec/issues/238): "objects without property bags"

    Define a `propertyBag` type and DRY out existing property bags to use it.

    In every object that does not yet have a property bag:

    - Add a `properties` property of type `propertyBag`

- [Issue #240](https://github.com/oasis-tcs/sarif-spec/issues/240): "Consider removing type inconsistency with message property in exception object"

    In the `exception` object:

    - Change the type of the `message` property from `string` to `message`.

- [Issue #242](https://github.com/oasis-tcs/sarif-spec/issues/242): "Rename startTime/endTime to startTimeUtc and endTimeUtc"

    In the `invocation` object:

    - Rename the `startTime` property to `startTimeUtc`
    - Rename the `endTime` property to `endTimeUtc`

    In the `file` object:

    - Rename the `lastModifiedTime` property to `lastModifiedTimeUtc`

    In the `versionControlDetails` object:

    - Rename the `timestamp` property to `asOfTimeUtc`

    In the `threadFlowLocation` object:

    - Rename the `timestamp` property to `executionTimeUtc`

    In the `notification` object:

    - Rename the `time` property to `timeUtc`

- [Issue #243](https://github.com/oasis-tcs/sarif-spec/issues/243): "Consider making file.hashes a dictionary" and [Issue #251](https://github.com/oasis-tcs/sarif-spec/issues/251): "We don't need a hash object"

    Remove the `hash` object.

    In the `files` object:

    - Change the type of the `hashes` property from `hash[]` to `object` with `string`-valued properties.

- [Issue #244](https://github.com/oasis-tcs/sarif-spec/issues/244): "Rename versionControlDetails.uri to repositoryUri"

    In the `versionControlDetails` object:

    - Rename the `uri` property to `repositoryUri`.

- [Issue #217](https://github.com/oasis-tcs/sarif-spec/issues/217): "Consider adding a discrete 'id' object"

    Define the `runAutomationDetails` object with the following properties:

    - `description` of type `message`, optional.
    - `instanceId` of type `string`, optional.
    - `instanceGuid` of type `string`, optional.
    - `correlationGuid` of type `string`, optional.

    In the `run` object:

    - Remove the `instanceGuid` property.
    - Remove the `correlationGuid` property.
    - Remove the `logicalId` property.
    - Remove the `automationLogicalId` property.
    - Remove the `description` property.
    - Add an `id` property of type `runAutomationDetails`, optional.
    - Add an `aggregationIds` property of type `runAutomationDetails[]`, optional.

- [Issue #219](https://github.com/oasis-tcs/sarif-spec/issues/219): "Delete 'threadFlowLocation.step' property"

    In the `threadFlowLocation` object:

    - Remove the `step` property.

- [Issue #250](https://github.com/oasis-tcs/sarif-spec/issues/250): "Consider result-level URI for alternate viewer"

    In the `result` object:

    - Add a `hostedViewerUri` property of type `string` in `uri` format, optional.

## Changes approved but not yet applied to the schema.

- [Issue #169](https://github.com/oasis-tcs/sarif-spec/issues/169): "Decide how to handle uncommon line break characters"

    In the `run` object:

    - Add a `newlineSequences` property of type `string[]`, optional, minItems: 1, default `[ "\r\n", "\n" ]`

- [Issue #188](https://github.com/oasis-tcs/sarif-spec/issues/188): "Consider specifying an implied default value when columnKind is missing"

    In the `run` object:

    - Specify `unicodeCodePoints` as the default for `columnKind`.

- [Issue #223](https://github.com/oasis-tcs/sarif-spec/issues/223): "Remove uniqueItems constraint from result.locations"

    In the `result` object:

    - On the `locations` property, remove the `uniqueItems` constraint.

- [Issue #256](https://github.com/oasis-tcs/sarif-spec/issues/256): "Make Run.Files an array"

    In the `run` object:

    - Change the type of the `files` property from `object` with `file`-valued properties to `file[]`.

    - Change the type of the `logicalLocations` property from `object` with `logicalLocation`-valued properties to `logicalLocation[]`.

    In the `resources` object:

    - Change the type of the `rules` property from `object` with `rule`-valued properties to `rule[]`.

    In the `fileLocation` object:

    - Add a `fileIndex` property of type `integer`, optional.

    In the `result` object:

    - Add a `ruleIndex` property of type `integer`, optional.

    In the `notification` object:

    - Add a `ruleIndex` property of type `integer`, optional.

    In the `location` object:

    - Add a `logicalLocationIndex` property of type `integer`, optional.

    In the `file` object:

    - Remove the `parentKey` property.

    - Add a `parentIndex` property of type `integer`, optional.

    In the `logicalLocation` object:

    - Remove the `parentKey` property.

    - Add a `parentIndex` property of type `integer`, optional.

    - Make the `name` property required.

    In the `rule` object:

    - Make the `id` property required.

- [Issue #267](https://github.com/oasis-tcs/sarif-spec/issues/267): "Allow an external file to contain multiple properties"

    Rename the `externalFile` object to `externalPropertyFile`.

    Rename the `externalizedProperty` object to `externalProperties`.

    In the `run` object:

    - Rename the `externalFiles` property to `externalPropertyFiles`.

- [Issue #269](https://github.com/oasis-tcs/sarif-spec/issues/269): "Add optional "itemCount" property to externalPropertyFile"

    In the `externalPropertyFile` object:

    - Add an `itemCount` property of type `integer`, `minValue: 1`, optional.

- [Issue #272](https://github.com/oasis-tcs/sarif-spec/issues/272): "Request: provide 'first seen' timestamp for results"

    Define the `resultProvenance` object with the following properties:

    - `firstDetectionTimeUtc` of type `string` in `date-time` format, optional.
    - `lastDetectionTimeUtc` of type `string` in `date-time` format, optional.
    - `firstDetectionRunInstanceGuid` of type `string`, optional.
    - `lastDetectionRunInstanceGuid` of type `string`, optional.

- [Issue #274](https://github.com/oasis-tcs/sarif-spec/issues/274): "Rename fileVersion to dottedQuadFileVersion and specify format constraint"

    In the `file` object:

    - Rename the `fileVersion` property to `dottedQuadFileVersion`.

- [Issue #279](https://github.com/oasis-tcs/sarif-spec/issues/279): "logicalLocation.kind: remove 'package'"

    In the `logicalLocation` object:

    - Modify the _comment_ on the `kind` property to remove mention of `"package"`.
    (The property is a `string`, not an `enum`, so only the comment needs to change.)

- [Issue #280](https://github.com/oasis-tcs/sarif-spec/issues/280): "Provide optional result.rank value of 0.0 to 100.0"

    In the `result` object:

    - Add a `rank` property of type `number`, `optional`, `minValue: 0.0`, `maxValue: 100.0`.

    In the `ruleConfiguration` object:

    - Add a `defaultRank` property of type `number`, `optional`, `minValue: 0.0`, `maxValue: 100.0`.

- [Issue #283](https://github.com/oasis-tcs/sarif-spec/issues/283): "result.message SHALL be present constraint should be added to schema"

    In the `result` object:

    - Add `message` to the list of required properties.

- [Issue #285](https://github.com/oasis-tcs/sarif-spec/issues/285): "Provide a mechanism to associate a result with an invocation."

    In the `resultProvenance` object:

    - Add an `invocationIndex` property of type `integer`, optional.

- [Issue #288](https://github.com/oasis-tcs/sarif-spec/issues/288): "ruleConfiguration.defaultLevel should not contain an 'open' value"

    In the `ruleConfiguration` object:

    - Remove the value `"open"` from the `enum` on the `defaultLevel` property.

## Changes not yet approved

- [Issue #202](https://github.com/oasis-tcs/sarif-spec/issues/202): "Restore threadFlowLocation.kind"

    In the `threadFlowLocation` object:

    - Add a `kind` property of type `string[]`, optional.

- [Issue #268](https://github.com/oasis-tcs/sarif-spec/issues/268): "Add result.useful and result.suppressionReasons"

    In the `result` object:

    - Add a `suppressionReasons` property of type `string[]`, optional, with enumerated values `"falsePositive"`, `"unshipped"`, `"testCode"`, `"deprecated"`, `"external"`.

    - Add a `useful` property of type `string`, optional, with enumerated values `"useful"`, `"notUseful"`, and `"unspecified"`.

- [Issue #270](https://github.com/oasis-tcs/sarif-spec/issues/270): "Schema needs to be carefully scrubbed for minItems and uniqueItems use for all arrays"

    To bring the schema into conformance with this issue, ensure that the attributes of each array-valued property match the [table](https://github.com/oasis-tcs/sarif-spec/issues/270#issuecomment-443353144) in the issue.