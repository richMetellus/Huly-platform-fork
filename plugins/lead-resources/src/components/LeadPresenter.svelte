<!--
// Copyright © 2020, 2021 Anticrm Platform Contributors.
// Copyright © 2021 Hardcore Engineering Inc.
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
  import type { Lead } from '@hcengineering/lead'
  import { Icon, tooltip } from '@hcengineering/ui'
  import { DocNavLink, ObjectMention } from '@hcengineering/view-resources'
  import lead from '@hcengineering/lead'
  import { getEmbeddedLabel } from '@hcengineering/platform'
  import { ObjectPresenterType } from '@hcengineering/view'

  export let value: Lead
  export let inline: boolean = false
  export let disabled: boolean = false
  export let accent: boolean = false
  export let noUnderline: boolean = false
  export let shouldShowAvatar: boolean = true
  export let type: ObjectPresenterType = 'link'
</script>

{#if value}
  {#if inline}
    <ObjectMention object={value} {disabled} />
  {:else if type === 'link'}
    <DocNavLink object={value} {disabled} {noUnderline} {accent}>
      <div class="flex-presenter">
        {#if shouldShowAvatar}
          <div class="icon"><Icon icon={lead.icon.Lead} size={'small'} /></div>
        {/if}
        <span class="label nowrap" class:no-underline={noUnderline || disabled} class:fs-bold={accent}>
          {value.identifier}
        </span>
      </div>
    </DocNavLink>
  {:else if type === 'text'}
    <span class="overflow-label" use:tooltip={{ label: getEmbeddedLabel(value.title) }}>{value.identifier}</span>
  {/if}
{/if}
