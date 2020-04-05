---
title: "虚拟 DOM"
date: 2019-11-02T08:56:04+08:00
draft: false
---

#### 虚拟DOM

根据真实的 DOM 结构创建一个和真实DOM 一一映射的数据结构来进行操作，操作完再把数据结构的变化反映到真实的DOM 中。
作用：当DOM 结构的改变或操作很细微的时候，使用虚拟DOM 可以避免因微小改变而重新渲染一整个DOM 结构，类似于打补丁。

```
class VNode {
  constructor(tag, children, text) {
    this.tag = tag
    this.text = text
    this.children = children
  }

  render() {
    if(this.tag === '#text') {
      return document.createTextNode(this.text)
    }
    let el = document.createElement(this.tag)
    this.children.forEach(vChild => {
      el.appendChild(vChild.render())
    })
    return el
  }
}

function v(tag, children, text) {
  if(typeof children === 'string') {
    text = children
    children = []
  }
  return new VNode(tag, children, text)
}


/*

let nodesData = {
  tag: 'div',
  children: [
    {
      tag: 'p',
      children: [
        {
          tag: 'span',
          children: [
            {
              tag: '#text',
              text: 'xiedaimala.com'
            }
          ]
        }
      ]
    },
    {
      tag: 'span',
        children: [
          {
            tag: '#text',
            text: 'jirengu.com'
          }
        ]
    }
  ]
}

 */


let vNodes = v('div', [
      v('p', [
        v('span', [ v('#text', 'xiedaimala.com') ] )
        ]
      ),
      v('span', [
        v('#text',  'jirengu.com')
        ])
    ]
  )
console.log(vNodes.render())

function patchElement(parent, newVNode, oldVNode, index = 0) {
  if(!oldVNode) {
    parent.appendChild(newVNode.render())
  } else if(!newVNode) {
    parent.removeChild(parent.childNodes[index])
  } else if(newVNode.tag !== oldVNode.tag || newVNode.text !== oldVNode.text) {
    parent.replaceChild(newVNode.render(), parent.childNodes[index])
  }  else {
    for(let i = 0; i < newVNode.children.length || i < oldVNode.children.length; i++) {
      patchElement(parent.childNodes[index], newVNode.children[i], oldVNode.children[i], i)
    }
  }
}


let vNodes1 = v('div', [
      v('p', [
        v('span', [ v('#text', 'xiedaimala.com') ] )
        ]
      ),
      v('span', [
        v('#text',  'jirengu.com')
        ])
    ]
  )

let vNodes2 = v('div', [
      v('p', [
        v('span', [ 
          v('#text', 'xiedaimala.com') 
          ] )
        ]
      ),
      v('span', [
        v('#text',  'jirengu.coms'),
        v('#text',  'ruoyu')
        ])
    ]
  )
const root = document.querySelector('#root')
patchElement(root, vNodes1)

```