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
  import { Analytics } from '@hcengineering/analytics'
  import { Card, CardEvents, cardId, MasterTag } from '@hcengineering/card'
  import core, { Class, Data, Doc, fillDefaults, MarkupBlobRef, Ref, SortingOrder } from '@hcengineering/core'
  import { translate } from '@hcengineering/platform'
  import { getClient } from '@hcengineering/presentation'
  import { makeRank } from '@hcengineering/rank'
  import {
    Button,
    deviceOptionsStore as deviceInfo,
    getCurrentLocation,
    IconAdd,
    navigate,
    Scroller
  } from '@hcengineering/ui'
  import { NavFooter, NavHeader, SavedView } from '@hcengineering/workbench-resources'
  import card from '../plugin'
  import TagHierarchy from './TagHierarchy.svelte'

  export let _class: Ref<Class<Doc>> | undefined
  export let classes: MasterTag[] = []
  export let allClasses: MasterTag[] = []

  const client = getClient()
  const hierarchy = client.getHierarchy()

  async function createCard (): Promise<void> {
    if (_class === undefined) return
    const lastOne = await client.findOne(card.class.Card, {}, { sort: { rank: SortingOrder.Descending } })
    const title = await translate(card.string.Card, {})

    const data: Data<Card> = {
      title,
      rank: makeRank(lastOne?.rank, undefined),
      content: '' as MarkupBlobRef,
      parentInfo: [],
      blobs: {}
    }

    const filledData = fillDefaults(hierarchy, data, _class)

    const _id = await client.createDoc(_class, core.space.Workspace, filledData)

    Analytics.handleEvent(CardEvents.CardCreated)

    const loc = getCurrentLocation()
    loc.path[3] = _id
    loc.path.length = 4
    navigate(loc)
  }

  let menuSelection: boolean = false
</script>

<div
  class="antiPanel-navigator {$deviceInfo.navigator.direction === 'horizontal' ? 'portrait' : 'landscape'} border-left"
  class:fly={$deviceInfo.navigator.float}
>
  <div class="antiPanel-wrap__content hulyNavPanel-container">
    <NavHeader label={card.string.Cards} />

    <div class="antiNav-subheader">
      <Button
        icon={IconAdd}
        label={card.string.CreateCard}
        disabled={allClasses.length === 0 || _class === undefined || _class === card.class.Card}
        justify={'left'}
        width={'100%'}
        kind={'primary'}
        gap={'large'}
        on:click={createCard}
      />
    </div>

    <SavedView alias={cardId} on:select={(res) => (menuSelection = res.detail)} />

    {#if classes.length > 0}
      <div class="antiNav-divider line" />
      <Scroller shrink>
        <TagHierarchy bind:_class deselect={menuSelection} {classes} {allClasses} on:select />
      </Scroller>
    {/if}

    <div class="mt-2" />

    <NavFooter split />
  </div>
</div>
