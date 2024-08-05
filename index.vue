<template>
        <div class="tree_wrapper">
                <el-input v-if="$props.isShowFilter" placeholder="输入关键字进行过滤" v-model="filterText"
                        suffix-icon="el-icon-search">
                </el-input>
                <el-tree ref="tree" :data="$props.dataSource" :props="$props.defaultProps"
                        @node-click="clickTreeItemAction" node-key="id" highlight-current
                        :default-expanded-keys="[...$props.defaultExpandedkeys]" :filter-node-method="filterNode">
                        <span class="custom-tree-node" slot-scope="{ node, data }">
                                <span style="display: flex; align-items: center">
                                        <img :src="$props.icon || require('../images/12.png')"
                                                style="width: 16px; height: 16px; margin-right: 4px"
                                                v-if="data.children" />
                                        <el-tooltip class="item" effect="dark" :content="node.label"
                                                placement="top-start">
                                                <span class="jhsdu"
                                                        :style="{ width: data.children ? '100%' : '110px' }">{{
                        node.label }}</span>
                                        </el-tooltip>
                                </span>
                                <span class="isShowTreeIcon" v-if="$props.isShowTreeIcon">
                                        <el-button type="text" size="mini"
                                                @click.stop="() => updateTreeItemAction(node, data)"
                                                style="color: #666666">
                                                <i class="el-icon-edit-outline"></i>
                                        </el-button>
                                        <el-button type="text" size="mini"
                                                @click.stop="() => delTreeItemAction(data.id, data)"
                                                style="color: #666666">
                                                <i class="el-icon-delete"></i>
                                        </el-button>
                                </span>
                        </span>
                </el-tree>
        </div>
</template>

<script>
export default {
        name: "MyrTee",
        props: {
                // 数据源
                dataSource: {
                        type: Array,
                        default: [],
                },

                // 自定义图标
                icon: {
                        type: String,
                        default: "",
                },
                // 是否支持搜索
                isShowFilter: {
                        type: Boolean,
                        default: false,
                },
                // 是否展示树操作按钮-编辑，删除
                isShowTreeIcon: {
                        type: Boolean,
                        default: false,
                },
                // 默认展开
                defaultExpandedkeys:{
                        type: Array,
                        default: ()=>{return []},
                },
                // 默认选中第几项,穿null不选中
                defaultActived: {
                        type: Number,
                        default: 0, // 第一个
                },
                // 字段
                defaultProps: {
                        type: Object,
                        default: {},
                },
                // 编辑事件
                updateTreeItem: {
                        type: Function,
                        default: () => { },
                },
                // 删除事件
                delTreeItem: {
                        type: Function,
                        default: () => { },
                },
                // 点击事件
                clickTreeItem: {
                        type: Function,
                        default: () => { },
                },
        },

        data() {
                return {
                        filterText: "", //树
                        treeNodeId: "", //树
                        data: [],
                };
        },
        computed: {
                initChoose() {
                        const { defaultActived, dataSource } = this.$props
                        return {
                                defaultActived, 
                                dataSource 
                        }
                }
        },

        watch: {
                filterText(val) {
                        this.$refs.tree.filter(val);
                },
                initChoose(value) {
                        const { defaultActived, dataSource } = value
                        this.$nextTick(() => {
                                dataSource[defaultActived] &&  this.$refs.tree.setCurrentKey(dataSource[defaultActived].id);
                        });
                },
        },
        methods: {
                filterNode(value, data) {
                        if (!value) return true;
                        return data[this.$props.defaultProps.label].indexOf(value) !== -1;
                },
                updateTreeItemAction(node, data) {
                        this.$props.updateTreeItem && this.$props.updateTreeItem(node, data);
                },
                delTreeItemAction(id, data) {
                        this.$props.delTreeItem && this.$props.delTreeItem(id, data);
                },
                clickTreeItemAction(data) {
                        this.$props.clickTreeItem && this.$props.clickTreeItem(data);
                },
        },
};
</script>

<style lang="scss" scoped>
::v-deep .el-tree--highlight-current .el-tree-node.is-current>.el-tree-node__content {
        color: #4a7dff !important;
        font-weight: 700;
        height: 32px !important;
}

::v-deep .el-tree-node__content {
        height: 32px !important;
}

.tree_wrapper {
        height: calc(100vh - 160px);
        overflow-y: scroll;

        .node_label {
                font-size: 12px;
        }
}

::-webkit-scrollbar {
        display: none !important;
}

.tree_wrapper {
        height: calc(100vh - 140px);
        overflow-y: scroll;

        .node_label {
                font-size: 12px;
        }

        ::v-deep .el-button--text {
                padding: 7px 0 !important;
        }
}

.custom-tree-node {
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 12px;
        padding-right: 8px;
        width: 200px;
        overflow: hidden;

        .jhsdu {
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
                max-width: 120px;
        }

        ::v-deep {
                .el-button--mini {
                        padding: 0 !important;
                }
        }

        .el-button+.el-button {
                margin-left: 5px !important;
        }

        .isShowTreeIcon {
                display: none;
        }

        &:hover {
                .isShowTreeIcon {
                        display: block;
                }
        }
}
</style>
