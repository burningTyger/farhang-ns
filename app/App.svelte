<page actionBarHidden="true">
    <stackLayout padding="5">
        <label text="Farhang.im durchsuchen"></label>
        <textField bind:text="{term}" hint="Deutsch oder persisch" />
        {#if lemmas.length === 0}
            <label text="Farhang.im" class="home"></label>
        {/if}
        <listView items="{lemmas}" style="height:75%" on:itemTap="{showLemma}">
            <Template let:item>
                {#if item.id === selectedItem.id}
                    {#each translations as t}
                        <label class="list-group-item-text list-item active" text="{`${t.source}: ${t.target}`}" textWrap="true"/>
                    {/each}
                {:else}
                    <label class="list-group-item-heading list-item" text="{item.lemma}" textWrap="true" />
                {/if}
            </Template>
        </listView>
    </stackLayout>
</page>

<script>
    import { Template } from 'svelte-native/components';
    import * as http from 'tns-core-modules/http'

    let term = ''
    let lemmas = []
    let translations
    let selectedItem = {}
    $:  if (term.length > 1) {
        http.getJSON(`https://api.farhang.im/a/${term}`)
            .then(res => setLemmas(res))
    }
    $: if (term.length === 0) lemmas = []
    function setLemmas(res) {
        lemmas = res
    }
    function showLemma (lemma) {
        selectedItem = lemma.view.bindingContext;
        http.getJSON(`https://api.farhang.im/g/${selectedItem.id}`)
            .then(res => {
                translations = res
                lemmas = Array.from(lemmas)
            })
    }
</script>

<style>
textField {
  font-size: 20;
  color:black;
  margin: 5;
  }
.list-item {
  font-size: 20;
  color: black;
  margin-left: 20;
  padding-top: 5;
  padding-bottom: 10;
}
.list-item.active {
  font-weight: bold;
  color: black;
}

.home {
  font-size: 40;
  text-align: center;
}
</style>