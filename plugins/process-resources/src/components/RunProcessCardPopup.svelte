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
  import { Card as CardPopup, getClient } from '@hcengineering/presentation'
  import plugin from '../plugin'
  import { Card } from '@hcengineering/card'
  import { CardSelector } from '@hcengineering/card-resources'
  import { Dropdown, Label, ListItem, Loading } from '@hcengineering/ui'
  import { Process } from '@hcengineering/process'
  import { createEventDispatcher } from 'svelte'
  import core, { Ref } from '@hcengineering/core'

  export let value: Ref<Process> | undefined

  const client = getClient()

  let process: Ref<Process> | undefined = value
  let card: Ref<Card> | undefined

  const processes = client.getModel().findAllSync(plugin.class.Process, {})

  const items: ListItem[] = processes.map((p) => ({ label: p.name, _id: p._id }))

  const dispatch = createEventDispatcher()

  async function runProcess (): Promise<void> {
    if (process === undefined || card === undefined) return
    await client.createDoc(plugin.class.Execution, core.space.Workspace, {
      process,
      currentState: null,
      card,
      done: false,
      rollback: {},
      currentToDo: null,
      assignee: null
    })
    dispatch('close')
  }

  $: selectedProcess = processes.find((p) => p._id === process)
</script>

<CardPopup
  label={plugin.string.RunProcess}
  canSave={process !== undefined && card !== undefined}
  okAction={runProcess}
  width={'menu'}
  on:close
>
  <div class="mb-4">
    <Dropdown
      {items}
      kind={'regular'}
      size={'medium'}
      placeholder={plugin.string.Process}
      disabled={value !== undefined}
      selected={items.find((i) => i._id === process)}
      on:selected={(e) => {
        process = e.detail._id
        card = undefined
      }}
    />
  </div>
  {#if selectedProcess !== undefined}
    <CardSelector kind={'regular'} size={'medium'} bind:value={card} _class={selectedProcess.masterTag} />
  {/if}
</CardPopup>
