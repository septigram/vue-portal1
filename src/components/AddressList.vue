<template>
  <div class="addressList">
    <el-collapse v-model="visible">
      <el-collapse-item name="address">
        <template slot="title">
          ■ ブックマーク
        </template>
        <el-button style="float:right;" circle @click='flipEditMode()' :type='editMode ? "success" : "normal"' title="編集モード" size="mini"><fa icon="cog"/></el-button>
        <br style="clear:both;"/>
        <draggable v-model='addressBook' :disabled='!editMode' :group='{ name: "addressBook"}' tag="ul" ghost-class="ghost" @change='updateTargetJsonAndSave()'>
          <template v-for='(b, bi) in addressBook'>
            <li class="category" :style='{ background: b.color, color: "#fff" }' :key='bi'>
              <div class="categoryLabel">
                <template v-if="editMode"><fa icon="align-justify" class="handle" style="font-size:small;"/></template>
                <template v-else>■</template>
                {{b.label}}
                <transition name="editbutton">
                  <el-button size="mini" style="padding: 4px;" @click='editCategory(b)' v-if='editMode'><fa icon="edit"/></el-button>
                </transition>
              </div>
              <draggable v-model='b.links' :disabled='!editMode' :group='{ name: "b.links" }' tag="ul" ghost-class="ghost" @change='updateTargetJsonAndSave()'>
                <transition-group>
                  <template v-for='(l, lli) in b.links'>
                    <li class="address" :key='lli'>
                      <template v-if='editMode'>
                        {{l.label}}
                      </template>
                      <template v-else>
                        <a :href='l.url' :title='l.title' :target='l.target'>{{l.label}}</a>
                      </template>
                      <transition name="editbutton">
                        <el-button v-if='editMode' size="mini" style="padding: 4px;" @click='editLink(l)'><fa icon="edit"/></el-button>
                      </transition>
                    </li>
                  </template>
                </transition-group>
              </draggable>
              <transition name="editbutton">
                <el-button size="mini" style="padding: 4px;" @click='addLink(b)' v-if='editMode'><fa icon="plus"/></el-button>
              </transition>
            </li>
          </template>
        </draggable>
        <transition name="editbutton">
          <el-button @click='addCategory()' v-if='editMode'><fa icon="plus"/>カテゴリー追加</el-button>
        </transition>
      </el-collapse-item>
    </el-collapse>
    <el-collapse >
      <transition name="toolcollapse">
        <el-collapse-item title="JSONデータ" v-if='editMode'>
          <el-input type="textarea" v-model='targetJson' :rows='20'></el-input>
          <el-button @click='updateJson()'>更新</el-button>
        </el-collapse-item>
      </transition>
    </el-collapse>
    <el-dialog title="ブックマーク編集" :visible.sync='showEditLink' @close='storeAddress()'>
      <templte v-if='targetLink'>
        <el-form label-width='12em'>
          <el-form-item label="表示名">
            <el-input v-model='targetLink.label'/>
          </el-form-item>
          <el-form-item label="URL">
            <el-input v-model='targetLink.url'/>
          </el-form-item>
          <el-form-item label="タイトル">
            <el-input v-model='targetLink.title'/>
          </el-form-item>
          <el-form-item label="説明">
            <el-input v-model='targetLink.description'/>
          </el-form-item>
          <el-form-item label="ターゲット">
            <el-input v-model='targetLink.target'/>
          </el-form-item>
          <el-form-item>
            <el-button @click='closeEdit()' type="success">閉じる</el-button>
            <el-button @click='deleteEdit(targetLink)' type="danger">削除する</el-button>
          </el-form-item>
        </el-form>
      </templte>
    </el-dialog>
    <el-dialog title="カテゴリ編集" :visible.sync='showEditCategory' @close='storeAddress()'>
      <templte v-if='targetCategory'>
        <el-form label-width='12em'>
          <el-form-item label="表示名">
            <el-input v-model='targetCategory.label'/>
          </el-form-item>
          <el-form-item label="色">
            <el-color-picker v-model='targetCategory.color'/>
          </el-form-item>
          <el-form-item>
            <el-button @click='closeEditCategory()' type="success">閉じる</el-button>
            <el-button @click='deleteEditCategory(targetCategory)' type="danger">削除する</el-button>
          </el-form-item>
        </el-form>
      </templte>
    </el-dialog>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'addressList',
  components: { draggable },
  data: function () {
    return {
      visible: ['address'],
      editMode: false,
      showEditLink: false,
      showEditCategory: false,
      targetLink: null,
      targetCategory: null,
      targetJson: '',
      trimObj: true,
      addressBook: []
    }
  },
  mounted: function () {
    this.loadAddress()
  },
  methods: {
    editLink: function (link) {
      // eslint-disable-next-line
      if (link) {
        this.targetLink = link
      }
      this.showEditLink = true
    },
    addLink: function (category) {
      if (category) {
        const target = {
          label: '',
          url: '',
          title: '',
          description: '',
          target: '_blank'
        }
        this.targetLink = target
        category.links.push(target)
      }
      this.showEditLink = true
    },
    closeEdit: function () {
      this.showEditLink = false
    },
    deleteEdit: function (targetLink) {
      this.$confirm('本当に[' + targetLink.label + ']を削除しますか？', 'Warning', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Cancel',
        type: 'warning'
      }).then(() => {
        this.addressBook.forEach((category) => {
          const index = category.links.indexOf(targetLink)
          if (index >= 0) {
            category.links.splice(index, 1)
          }
        })
        this.updateTargetJsonAndSave()
        this.showEditLink = false
      })
    },
    editCategory: function (category) {
      if (category) {
        this.targetCategory = category
      }
      this.showEditCategory = true
    },
    addCategory: function () {
      const target = {
        label: '',
        color: null,
        links: []
      }
      this.targetCategory = target
      this.addressBook.push(target)
      this.showEditCategory = true
    },
    closeEditCategory: function () {
      this.showEditCategory = false
    },
    deleteEditCategory: function (targetCategory) {
      this.$confirm('本当に[' + targetCategory.label + ']を削除しますか？', 'Warning', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Cancel',
        type: 'warning'
      }).then(() => {
        const index = this.addressBook.indexOf(targetCategory)
        if (index >= 0) {
          this.addressBook.splice(index, 1)
        }
        this.updateTargetJsonAndSave()
        this.showEditCategory = false
      })
    },
    storeAddress: function () {
      this.targetJson = this.convJson(this.addressBook, true)
      if (window.localStorage) {
        window.localStorage.setItem('address', this.targetJson)
      }
    },
    loadAddress: function () {
      if (window.localStorage) {
        this.targetJson = window.localStorage.getItem('address')
        if (this.targetJson) {
          this.addressBook = JSON.parse(this.targetJson)
        }
      }
    },
    updateJson: function () {
      this.addressBook = JSON.parse(this.targetJson)
      this.storeAddress()
    },
    updateTargetJson: function () {
      this.targetJson = this.convJson(this.addressBook, true)
    },
    updateTargetJsonAndSave: function () {
      this.targetJson = this.convJson(this.addressBook, true)
      this.storeAddress()
    },
    flipEditMode: function () {
      this.editMode = !this.editMode
    },
    convJson: function (obj, trimObj) {
      let v = JSON.stringify(obj, undefined, '\t')
      if (trimObj) {
        const lines = v.split('\n')
        const vs = []
        let ins = []
        let inObj = false
        for (let i = 0; i < lines.length; i++) {
          const line = lines[i]
          const token = line.trim()
          if (token.match(/".*": [^,]*,?[^{[]/)) {
            ins.push({ line: line, token: token })
          } else if (token == '{') {
            ins.push({ line: line, token: line })
            inObj = true
          } else if (token == '}' || token == '},') {
            ins.push({ line: line, token: token })
            if (inObj) {
              const vs2 = []
              ins.forEach((s) => vs2.push(s.token))
              vs.push(vs2.join(' '))
            } else {
              ins.forEach((s) => vs.push(s.line))
            }
            ins = []
            inObj = false
          } else {
            ins.forEach((s) => vs.push(s.line))
            ins = []
            vs.push(line)
          }
        }
        v = vs.join('\n')
      }
      return v
    }
  }
}
</script>

<style>
div.addressList {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
li.category {
  border-radius: 0.3em;
  padding: 0;
  margin: 0;
}
div.categoryLabel {
  display: inline-block;
  background: rgba(0,0,0,0.3);
  padding: 0.4em;
  border-radius: 0.4em;
  margin: 0.1em;
}
ul {
  display: inline-block;
  margin: 0;
  margin-block-start: 0;
  margin-block-end: 0;
  padding-inline-start: 0;
}
li.address {
  display: inline-block;
  color: #000;
  background: rgba(255,255,255,0.9);
  border: 1px solid #fff;
  border-radius: 0.4em;
  padding: 0.4em;
  margin: 0.1em;
}
li.address a {
  text-decoration: none;
  color: #069;
}
li.address a:hover {
  text-decoration: underline;
}
li.ghost {
  opacity: 0.5;
  background: #999;
}
.editbutton-enter-active, .editbutton-leave-active {
  transition: all 0.5s;
}
.editbutton-enter, .editbutton-leave-to {
  transform: scale(0);
}
</style>