<script lang="ts" setup>
import { useGovStore } from '@/stores';
import ProposalListItem from '@/components/ProposalListItem.vue';
import { ref, onMounted } from 'vue';
import PaginationBar from '@/components/PaginationBar.vue';
import { PageRequest } from '@/types';

const tab = ref('2');
const store = useGovStore();
const pageRequest = ref(new PageRequest())
console.log(store.proposals)
onMounted(() => {
    store.fetchProposals('2').then((x) => {
        if (x?.proposals?.length === 0) {
            tab.value = '3';
            store.fetchProposals('3');
        }
        store.fetchProposals('3');
        store.fetchProposals('4');
        store.fetchProposals('5');
    });
});

const changeTab = (val: '2' | '3' | '4'|  '5') => {
    tab.value = val;
};

function page(p: number) {
    pageRequest.value.setPage(p)
    store.fetchProposals(tab.value, pageRequest.value)
}

</script>
<template>
    <div>
        <div class="tabs tabs-boxed bg-transparent mb-4 text-center">
            <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === '2' }" @click="changeTab('2')">{{ $t('gov.voting') }}</a>
            <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === '3' }" @click="changeTab('3')">{{ $t('gov.passed') }}</a>
            <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === '4' }"
                @click="changeTab('4')">{{ $t('gov.rejected') }}</a>
          <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === '5' }" @click="changeTab('5')">{{ $t('Failed') }}</a>

        </div>
        <ProposalListItem :proposals="store?.proposals[tab]" />
        <PaginationBar :total="store?.proposals[tab]?.pagination?.total" :limit="pageRequest.limit" :callback="page" />
    </div>
</template>
<route>
  {
    meta: {
      i18n: 'governance',
      order: 2
    }
  }
</route>
