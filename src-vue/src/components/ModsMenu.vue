<template>
    <nav class="fc_mods__menu">
        <el-menu
            default-active="1"
            text-color="#fff"
        >
            <h5>{{ $t('menu.mods') }}</h5>
            <el-menu-item index="1" @click="$emit('showLocalMods', true)">
                <el-icon><Folder /></el-icon>
                <span>{{ $t('mods.menu.local') }}</span>
            </el-menu-item>

            <!-- Display a badge if there are some outdated Thunderstore mods -->
            <el-menu-item v-if="outdatedThunderstoreModsCount !== 0" index="2" @click="$emit('showLocalMods', false)">
                <el-badge :value="outdatedThunderstoreModsCount" class="item" type="warning">
                    <el-icon><Connection /></el-icon>
                    <span>{{ $t('mods.menu.online') }}</span>
                </el-badge>
            </el-menu-item>
            <el-menu-item v-else index="2" @click="$emit('showLocalMods', false)">
                <el-icon><Connection /></el-icon>
                <span>{{ $t('mods.menu.online') }}</span>
            </el-menu-item>

            <!-- Search inputs -->
            <h5>{{ $t('mods.menu.filter') }}</h5>
            <el-input v-model="$store.state.search.searchValue" :placeholder="$t('mods.menu.search')" clearable />
            <el-select
                v-if="!showingLocalMods"
                v-model="$store.state.search.sortValue"
                :placeholder="$t('mods.menu.sort_mods')"
                popper-class="fc_mods__select-dropdown"
            >
                <el-option
                    v-for="item of sortValues"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                />
            </el-select>
            <el-select
                v-if="!showingLocalMods"
                v-model="$store.state.search.selectedCategories"
                :placeholder="$t('mods.menu.select_categories')"
                popper-class="fc_mods__select-dropdown"
            >
                <el-option
                    v-for="item in $store.state.thunderstoreModsCategories"
                    :key="item"
                    :label="item"
                    :value="item"
                />
            </el-select>

        </el-menu>
    </nav>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { SortOptions } from '../utils/SortOptions.d';
import { isThunderstoreModOutdated } from "../utils/thunderstore/version";
import { ThunderstoreMod } from "../../../src-tauri/bindings/ThunderstoreMod";

export default defineComponent({
    name: 'ModsMenu',
    props: {
        showingLocalMods: {
            required: true,
            type: Boolean
        }
    },
    mounted() {
        this.$store.state.search.sortValue = this.sortValues[3].value;
    },
    computed: {
        outdatedThunderstoreModsCount(): number {
            return this.$store.state.thunderstoreMods
                .filter((mod: ThunderstoreMod) => isThunderstoreModOutdated(mod))
                .length;
        },
        sortValues(): { label: string, value: string }[] {
            return Object.keys(SortOptions).map((key: string) => ({
                value: key,
                label: this.$t('mods.menu.sort.' + Object.values(SortOptions)[Object.keys(SortOptions).indexOf(key)])
            }));
        }
    }
})
</script>

<style scoped>
.fc_mods__menu {
    display: flex;
    max-width: 222px;
    min-width: 222px;
    padding: 10px;
}

.fc_mods__menu h5 {
    margin: 8px 0 16px 5px;
}

.fc_mods__menu h5:not(:first-child) {
    margin-top: 32px;
}

.fc_mods__menu > .el-menu {
    background-color: transparent;
    border: none;
    width: 100%;
}

.fc_mods__menu > .el-menu > .el-menu-item {
    height: 32px;
    margin-bottom: 5px;
    border-radius: 5px;
    color: #e2e6e7;
}

.fc_mods__menu > .el-menu > .el-menu-item:hover {
    background-color: #4e4e4e3b;
}

.fc_mods__menu > .el-menu > .el-menu-item.is-active {
    color: white;
    background-color: #4e4e4e7a;
}

.el-select {
    width: 100%;
    margin-top: 5px;
}

/* Outdated thunderstore mods count */
.el-badge {
    width: 100%;
}
.el-badge:deep(.el-badge__content) {
    top: 28px !important;
}

.fc_mods__menu :deep(.el-input__wrapper),
.fc_mods__menu :deep(.el-select__wrapper) {
    background-color: var(--fc-panel-bg-dark);
    box-shadow: none;
    border: none !important;          /* remove border */
}

/* text color in the control (not the dropdown list) */
.fc_mods__menu :deep(.el-input__inner),
.fc_mods__menu :deep(.el-select__selected-item) {
    color: #fff;
}

</style>

<style>
/* dropdown popper (teleported into body) */
.el-popper.fc_mods__select-dropdown {
    background-color: var(--fc-panel-bg-dark) !important;
    border: none !important;
    box-shadow: none !important;
}

/* HIDE the little diamond arrow on top of the dropdown */
.el-popper.fc_mods__select-dropdown .el-popper__arrow {
    display: none !important;
}

/* inner scroll area */
.fc_mods__select-dropdown .el-scrollbar,
.fc_mods__select-dropdown .el-select-dropdown__wrap,
.fc_mods__select-dropdown .el-scrollbar__view {
    background-color: transparent !important;
}

/* dropdown items text */
.fc_mods__select-dropdown .el-select-dropdown__item,
.fc_mods__select-dropdown .el-select-dropdown__item span {
    color: #fff !important;
}

/* selected item background */
.fc_mods__select-dropdown .el-select-dropdown__item.is-selected {
    background-color: #4e4e4e7a !important;
}
</style>
