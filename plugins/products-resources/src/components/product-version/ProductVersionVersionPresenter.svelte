<!--
//
// Copyright © 2024 Hardcore Engineering Inc.
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
//
-->

<script lang="ts">
  import { ProductVersion } from '@hcengineering/products'
  import { DocNavLink, ObjectMention } from '@hcengineering/view-resources'

  import { getProductVersionVersion } from '../../utils'

  export let value: ProductVersion | undefined
  export let inline: boolean = false
  export let disabled: boolean = false
  export let accent: boolean = false
  export let noUnderline: boolean = false

  $: version = value !== undefined ? getProductVersionVersion(value) : ''
</script>

{#if value}
  {#if inline}
    <ObjectMention object={value} {disabled} title={version} />
  {:else}
    <DocNavLink object={value} {disabled} {noUnderline} {accent}>
      <div class="flex-presenter">
        <span class="label nowrap" class:no-underline={noUnderline || disabled} class:fs-bold={accent}>
          {version}
        </span>
      </div>
    </DocNavLink>
  {/if}
{/if}
