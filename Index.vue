<template>
  <div class="container" ref="container">
    <div>
      <canvas class="canvas" ref="canvas" width="500" height="500"></canvas>
    <canvas class="canvas" ref="canvas2" width="500" height="500"></canvas>
    </div>
    <div class="btn">
      <el-button type="primary" @click="toggle">{{isRefresh ? '停止' : '自动刷新'}}</el-button>
      <el-button type="primary" @click="draw">手动刷新</el-button>
    </div>
  </div>
</template>

<script>
import rough from 'roughjs/bundled/rough.esm.js'

export default {
  name: 'Index',
  data () {
    return {
      rc: null,
      ctx: null,
      offset: 5,
      isRefresh: false,
      timer: null
    }
  },
  mounted () {
    // this.demo()
    this.ctx = this.$refs.canvas2.getContext('2d')
    this.draw()
  },
  methods: {
    /**
     * @Author: 王林25
     * @Date: 2021-02-05 10:11:39
     * @Desc: 示例
     */
    demo () {
      this.rc = rough.canvas(this.$refs.canvas)
      this.rc.rectangle(100, 150, 300, 200, {
        roughness: 3,
        hachureGap: 15,
        fill: 'red',
        fillStyle: ''
      })
      this.rc.circle(195, 220, 40, {
        fill: 'red'
      })
      this.rc.circle(325, 220, 40, {
        fill: 'red'
      })
      this.rc.rectangle(225, 270, 80, 30, {
        fill: 'red',
        fillweight: 5
      })
      this.rc.line(200, 150, 150, 80, { roughness: 5 })
      this.rc.line(300, 150, 350, 80, { roughness: 2 })
    },
    /**
     * @Author: 王林25
     * @Date: 2021-02-05 10:13:13
     * @Desc: 切换
     */
    toggle () {
      if (this.isRefresh) {
        clearTimeout(this.timer)
        this.isRefresh = false
      } else {
        this.isRefresh = true
        this.refresh()
      }
    },
    /**
     * @Author: 王林25
     * @Date: 2021-02-05 10:11:45
     * @Desc: 刷新
     */
    refresh () {
      if (!this.isRefresh) {
        return
      }
      this.timer = setTimeout(() => {
        this.draw()
        this.refresh()
      }, 500)
    },
    /**
     * @Author: 王林25
     * @Date: 2021-02-05 10:12:22
     * @Desc: 绘制
     */
    draw () {
      this.ctx.clearRect(0, 0, 500, 500)
      // this.line(200, 150, 150, 80)
      // this.line(300, 150, 350, 80)
      this.rectangle(100, 150, 300, 200, {
        // fillStyle: 'red'
      })
      // let p = []
      // let num = Math.ceil(Math.random() * 5) + 2
      // for (let i = 0; i < num; i++) {
      //   p.push([Math.random() * 500, Math.random() * 500])
      // }
      // this.polygon(p, {
      //   fillStyle: 'red'
      // })
    },
    /**
     * @Author: 王林25
     * @Date: 2021-02-04 15:59:52
     * @Desc: 绘制线段
     */
    line (x1, y1, x2, y2) {
      this.drawDoubleLine(x1, y1, x2, y2)
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-05 11:04:01
     * @Desc: 绘制矩形
     */
    rectangle (x, y, width, height, opt = {}) {
      let points = [
        [x, y],
        [x + width, y],
        [x + width, y + height],
        [x, y + height]
      ]
      this.polygon(points, opt)
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-07 15:39:42
     * @Desc: 绘制多边形
     */
    polygon (points = [], opt = {}) {
      if (points.length < 3) {
        return
      }
      let len = points.length
      for (let i = 0; i < len - 1; i++) {
        this.line(points[i][0], points[i][1], points[i + 1][0], points[i + 1][1])
      }
      this.line(points[len - 1][0], points[len - 1][1], points[0][0], points[0][1])
      if (opt.fillStyle) {
        let lines = this.scanLines(points)
        lines.forEach((line) => {
          this.line(line[0][0], line[0][1], line[1][0], line[1][1])
        })
      }
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-05 11:11:52
     * @Desc: 绘制两条线段
     */
    drawDoubleLine (x1, y1, x2, y2) {
      // 绘制原线段
      // this.ctx.save()
      // this.ctx.beginPath()
      // this.ctx.moveTo(x1, y1)
      // this.ctx.lineTo(x2, y2)
      // this.ctx.strokeStyle = '#999'
      // this.ctx.stroke()
      // this.ctx.restore()

      let line1 = this._line(x1, y1, x2, y2)
      let line2 = this._line(x1, y1, x2, y2)
      // this.drawPoint(line1[4], line1[5])
      // this.drawPoint(line1[6], line1[7])
      this.drawLine(line1)
      this.drawLine(line2)
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-04 17:08:21
     * @Desc: 画线段
     */
    drawLine (line) {
      this.ctx.beginPath()
      this.ctx.moveTo(line[0], line[1])
      this.ctx.bezierCurveTo(
        line[4],
        line[5],
        line[6],
        line[7],
        line[2],
        line[3]
      )
      this.ctx.strokeStyle = '#000'
      this.ctx.stroke()
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-04 16:41:04
     * @Desc: 生成曲线
     */
    _line (x1, y1, x2, y2) {
      let result = []
      // 起始点
      result[0] = x1 + this.random(-this.offset, this.offset)
      result[1] = y1 + this.random(-this.offset, this.offset)
      // 终点
      result[2] = x2 + this.random(-this.offset, this.offset)
      result[3] = y2 + this.random(-this.offset, this.offset)
      // 两个控制点
      let xo = x2 - x1
      let yo = y2 - y1
      let rn = Math.random() >= 0.5
      let r = () => {
        if (rn) {
          return 0.2 + this.random(0, 0.2) // 限定在百分之20到百分之40的范围
        } else {
          return 0.6 + this.random(0, 0.2) // 限定在百分之60到百分之80的范围
        }
      }
      let r1 = r()
      let r2 = r()
      result[4] = x1 + xo * r1 + this.random(-2, 2)
      result[5] = y1 + yo * r1 + this.random(-2, 2)
      result[6] = x1 + xo * r2 + this.random(-2, 2)
      result[7] = y1 + yo * r2 + this.random(-2, 2)
      return result
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-05 10:02:26
     * @Desc: 绘制点
     */
    drawPoint (x, y) {
      this.ctx.beginPath()
      this.ctx.arc(x, y, 2, 0, 2 * Math.PI)
      this.ctx.fillStyle = 'red'
      this.ctx.fill()
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-04 16:35:58
     * @Desc: 返回一个范围内的随机数
     */
    random (min, max) {
      return Math.random() * (max - min) + min
    },

    /**
     * @Author: 王林25
     * @Date: 2021-02-07 15:31:18
     * @Desc: 扫描填充算法获取填充线段
     */
    scanLines (points) {
      let lines = []
      // 创建边表ET
      let edgeTable = []
      let _points = points.concat([[points[0][0], points[0][1]]])
      let len = _points.length
      for (let i = 0; i < len - 1; i++) {
        let p1 = _points[i]
        let p2 = _points[i + 1]
        if (p1[1] !== p2[1]) { // 过滤掉平行于x轴的线段
          let ymin = Math.min(p1[1], p2[1])
          edgeTable.push({
            ymin,
            xi: ymin === p1[1] ? p1[0] : p2[0], // 较低顶点的x值
            ymax: Math.max(p1[1], p2[1]),
            dx: (p2[0] - p1[0]) / (p2[1] - p1[1])// 线段的效率的倒数
          })
        }
      }
      // 对边表进行排序，按ymin递增
      edgeTable.sort((e1, e2) => {
        if (e1.ymin < e2.ymin) {
          return -1
        }
        if (e1.ymin > e2.ymin) {
          return 1
        }
        return 0
      })
      if (edgeTable.length <= 0) {
        return []
      }
      // 活动边表AET
      let activeEdgeTable = []
      // 开始扫描
      let y = edgeTable[0].ymin
      // 循环的终点是两个表都为空
      while (edgeTable.length > 0 || activeEdgeTable.length > 0) {
        // 从ET表里把当前扫描线的边添加到AET表里
        if (edgeTable.length > 0) {
          // 当前ET表里第一条ymin大于扫描线的边
          for (let i = 0; i < edgeTable.length; i++) {
            if (edgeTable[i].ymin <= y && edgeTable[i].ymax >= y) {
              let removed = edgeTable.splice(i, 1)
              activeEdgeTable.push(...removed)
              i--
            }
          }
        }
        // 从AET表里删除y=ymax的记录
        activeEdgeTable = activeEdgeTable.filter((item) => {
          return !(y >= item.ymax)
        })
        // 按xi从小到大排序
        activeEdgeTable.sort((e1, e2) => {
          if (e1.xi < e2.xi) {
            return -1
          } else if (e1.xi > e2.xi) {
            return 1
          } else {
            return 0
          }
        })
        if (activeEdgeTable.length > 1) {
          // 两两进行填充
          for (let i = 0; i < activeEdgeTable.length; i += 2) {
            if (i + 1 >= activeEdgeTable.length) {
              break
            }
            lines.push([
              [Math.round(activeEdgeTable[i].xi), y],
              [Math.round(activeEdgeTable[i + 1].xi), y]
            ])
          }
        }
        // 更新扫描线y
        y += 5
        // 更新活动边的xi
        activeEdgeTable.forEach((item) => {
          item.xi += item.dx * 5
        })
        // break
      }
      return lines
    }
  }
}
</script>

<style lang="less">
.container {
  width: 100%;
  height: 100%;

  .canvas {
    width: auto;
    height: auto;
    border: 1px solid #999;
  }

  .btn {
    display: flex;
    width: 1000px;
    justify-content: center;
  }
}
</style>
