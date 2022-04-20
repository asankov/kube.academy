# 04. Templating

Template - intermediate object used by Helm to generate the Kubernetes manifests.
It contains placeholders that will be filled by Helm to generate the final manifests.

Placeholders are indicated in Helm via double curly braces (template directives) - `{{ }}`.
For example:

```yaml
image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```

`.Values` is an object that points to the `values.yaml` file.
(It also includes the values passed via the CLI `--set` flag.)

Given this `values.yaml` file:

```yaml
image:
  repository: bitnami/jenkins
  tag: latest
```

Helm will generate the following manifest:

```yaml
image: bitnami/jenkins:latest
```

Other objects provided by Helm are:

- `.Release` - the properties of the release
- `.Chart` - the contents of `Chart.yaml`
- `.Capabilities`
- `.Templates`
- `.Files`

## Template functions

Inside the template directives we can use functions:

```yaml
  name: {{ .Release.Name }}-{{ .Chart.Name }}
```

can be replaced with:

```yaml
  name: {{ printf "%s-%s" .Release.Name .Chart.Name }}
```

Another function we can use is `default` which provides a default value, if such is not provided.
For example, the following template:

```yaml
value: {{ default "test" .Values.jenkinsUsername }}
```

will render as:

```yaml
value: test
```

if `jenkinsUsername` is empty in the `values.yaml` file.

Otherwise it will render `jenkinsUsername` for `value`:

```yaml
# values.yaml
jenkinsUsername: anton

# rendered.yaml
value: anton
```

The `quote` function will add quotes to whatever input it is provided:

```yaml
# template.yaml
value: {{ quote "test" }}

# rendered.yaml
value: "test"
```

In this example, it is useless, but we can also pipe Helm function Unix-style:

```yaml
value: {{ default "test" .Values.jenkinsUsername | quote }}
```

This will make sure that whatever input we have for `.Values.jenkinsUsername` (even if it contains whitespaces) it will be quoted and YAML-compatible:

```yaml
# values.yaml
jenkinsUsername: Anton Sankov

# template.yaml
value: {{ default "test" .Values.jenkinsUsername | quote }}

# rendered.yaml
value: "Anton Sankov"
```

Helm template are powered by the Go template engine.
A full reference to that can be found at <https://pkg.go.dev/text/template>.

## If-else

We can write if-else conditions in the templates.
This is done via the `{{ if <condition> }}` and `{{ else }}` directives.

For example, if we want to output a value only if that is present, otherwise output a default (basically implementing the `default` function) we can do it like this:

```yaml
{{- if .Values.jenkinsPassword }}
value: {{ .Values.jenkinsPassword }}
{{- else }}
value: testPassword
{{- end}}
```

the `-` after the curly braces (`{{`) tells Helm to remove all whitespaces and the new line before the expression.
We can also use it before the closing braces - `-}}`.

## Range

We can use the `range` control structure to iterate over a bunch of values.

For example, if we have the following `values.yaml` file:

```yaml
containerPorts:
  - name: http
    port: 8080
  - name: https
    port: 443
```

we can write the following template:

```yaml
ports:
  {{- range .Values.containerPorts }}
  - name: {{ .name }}
    port: {{ .port }}
  {{- end }}
```

this will output:

```yaml
ports:
  - name: http
    port: 8080
  - name: https
    port: 443
```

**NOTE:** When inside the `range` body the context is changed to the one of the element being iterated.
That is why we can say `{{ .name }}` and that will point to the `name` property of the current element.

## Template macros

These are function that we can reuse across our templates.
They live in the `_helpers.tpl` file.

This is a sample macro:

```yaml
{{/*
Create a default fully qualified app name.
We truncate at 63 chars because some Kubernetes name fields are limited to this (by the DNS naming spec).
If release name contains chart name it will be used as a full name.
*/}}
{{- define "kubeacademy.fullname" -}}
{{- if .Values.fullnameOverride }}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- $name := default .Chart.Name .Values.nameOverride }}
{{- if contains $name .Release.Name }}
{{- .Release.Name | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}
{{- end }}
```

that can be used inside a template like this:

```yaml
name: {{ template "kubeacademy.fullname" . }}
```

- `template` indicates that we are calling a macro
- `"kubeacademy.fullname"` is the name of the macro
- `.` is the argument we pass to the macro (in this case the whole context)
