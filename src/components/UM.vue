<template>
    <div class="um">
        um菜单
        <div :id="editor"></div>

    </div>
</template>
<script type="text/javascript">
    window.UMEDITOR_HOME_URL = process.env.NODE_ENV ==='development' ? '../../static/utf8-php/': '/manage/static/utf8-php/'
    
    require("../../static/utf8-php/umeditor.config.js");
    require("../../static/utf8-php/umeditor.min.js");
    export default {
        props: {
            value: {
                default: '',
                ue: null
            }
        }
        data() {
            return {
                editor: 'editor' + parseInt(Math.random() * 100),
            }
        },
        mounted() {
            this.$nextTick().then(()=>{
                UM.getEditor(this.editor);
            })
        },
        methods: {
            // 保存
            submits() {
                this.uedata = UM.getEditor(this.editor).getContent();
                this.$emit('input', this.uedata)
            },
            //写入
            getEditor() {
                let self = this;
                this.ue.ready(() => {
                    this.ue.setContent(this.value);
                    /*******************重写UEditor上传图片的方法***********************/
                    let dom = document.getElementsByClassName("edui-btn-image")[0];
                    dom.addEventListener("click", () => {
                        // $("#uploadImg").click();
                    });
                    this.ue.addListener('blur', (editor) => {
                        this.submits();
                    });
                })
            },
            insertPic() {
                this.ue.execCommand(`inserthtml`, `<img src="${this.picUrl}" />`)
                this.dialogVisible = false;
            }
        },
        // 销毁editor
        destroyed() {
            this.ue.destroy();
        },
        watch: {
            value(val, old) {
                this.ue.ready(() => {
                    this.ue.setContent(val);
                });
            },
            picUrl(val, newVal) {
                if (val !== newVal) {
                    this.insertPic();
                }
            }
        }
    }
</script>

<style type="text/css">
    @import '../../static/utf8-php/themes/default/css/umeditor.min.css'
</style>