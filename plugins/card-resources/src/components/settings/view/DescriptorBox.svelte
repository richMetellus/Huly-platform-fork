<!--
// Copyright © 2025 Hardcore Engineering Inc.
//
// Licensed under the Eclipse Public License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License. You may
// obtain a copy of the License at https://www.eclipse.org/legal/epl-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
//
// See the License for the specific language governing permissions and
// limitations under the License.
-->
<script lang="ts">
  import { Ref } from '@hcengineering/core'
  import { ObjectBox } from '@hcengineering/view-resources'
  import view, { ViewletDescriptor } from '@hcengineering/view'
  import { IntlString } from '@hcengineering/platform'

  export let value: Ref<ViewletDescriptor> | undefined = undefined
  export let withSingleViews: boolean = false
  export let label: IntlString
  $: supportedTypes = withSingleViews
    ? [view.viewlet.Table, view.viewlet.List, view.viewlet.Tree, view.viewlet.MasterDetail, view.viewlet.Document]
    : [view.viewlet.Table, view.viewlet.List, view.viewlet.Tree, view.viewlet.MasterDetail]
</script>

<ObjectBox
  _class={view.class.ViewletDescriptor}
  {label}
  showNavigate={false}
  bind:value
  docQuery={{
    _id: { $in: supportedTypes }
  }}
  on:change
/>
