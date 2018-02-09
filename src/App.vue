<template>
    <div class="box">
        <ul class="puzzle-wrap">
            <li 
                :class="{'puzzle': true, 'puzzle-empty': !puzzle}" 
                v-for="puzzle in puzzles" 
                v-text="puzzle"
                @click="moveFn($index)"
            ></li>
        </ul>
        <button class="btn btn-warning btn-block btn-reset" @click="render">重置游戏</button>
    </div>
</template>

<script>
export default {
    data () {
        return {
            puzzles: []
        }
    },
    methods: {

        // 重置渲染
        render () {
            let puzzleArr = [],
                i = 1

            // 生成包含1 ~ 15数字的数组
            for (i; i < 16; i++) {
                puzzleArr.push(i)
            }

            // 随机打乱数组
            //puzzleArr = puzzleArr.sort(() => {
            //    return Math.random() - 0.5
            //});

            // 页面显示
            this.puzzles = puzzleArr
            this.puzzles.push('')

            // 初始的空格位置是15
            let zeroindex = 15;
            for(i = 1;i < 1024;i++) {
                zeroindex = this.shuffle(zeroindex, this.puzzles);
            }
        },

        //按游戏规则随机打乱
        shuffle(index, puzzles) {
            // 获取空格位置及其上下左右的值
            let curNum = puzzles[index],
                leftNum = (index - 1 >= 0) ? puzzles[index - 1] : -1,
                rightNum = (index + 1 < 16) ? puzzles[index + 1] : -1,
                topNum = (index - 4 >= 0) ? puzzles[index - 4] : -1,
                bottomNum = (index + 4 < 16) ? puzzles[index + 4] : -1;

            // 随机让为空的位置和旁边的位置交换数值
            let r = Math.random();
            if (leftNum !== -1 && index % 4 && (r > 0.75)) {
                puzzles.$set(index - 1, curNum)
                puzzles.$set(index, leftNum)
                return index - 1;
            } else if (rightNum !== -1 && 3 !== index % 4 && (r < 0.25)) {
                puzzles.$set(index + 1, curNum)
                puzzles.$set(index, rightNum)
                return index + 1;
            } else if (topNum !== -1 && index > 3 &&  (r > 0.5)) {
                puzzles.$set(index - 4, curNum)
                puzzles.$set(index, topNum)
                return index - 4;
            } else if (bottomNum !== -1 && index < 12) {
                puzzles.$set(index + 4, curNum)
                puzzles.$set(index, bottomNum)
                return index + 4;
            }

            return index;
        },

        // 点击方块
        moveFn (index) {

            // 获取点击位置及其上下左右的值
            let curNum = this.puzzles[index],
                leftNum = this.puzzles[index - 1],
                rightNum = this.puzzles[index + 1],
                topNum = this.puzzles[index - 4],
                bottomNum = this.puzzles[index + 4]

            // 和为空的位置交换数值
            if (leftNum === '' && index % 4) {
                this.puzzles.$set(index - 1, curNum)
                this.puzzles.$set(index, '')
            } else if (rightNum === '' && 3 !== index % 4) {
                this.puzzles.$set(index + 1, curNum)
                this.puzzles.$set(index, '')
            } else if (topNum === '') {
                this.puzzles.$set(index - 4, curNum)
                this.puzzles.$set(index, '')
            } else if (bottomNum === '') {
                this.puzzles.$set(index + 4, curNum)
                this.puzzles.$set(index, '')
            }

            this.passFn()
        },

        // 校验是否过关
        passFn () {
            if (this.puzzles[15] === '') {
                const newPuzzles = this.puzzles.slice(0, 15)

                const isPass = newPuzzles.every((e, i) => e === i + 1)

                if (isPass) {
                    alert ('恭喜，闯关成功！')
                }
            }
        }
    },
    ready () {
        this.render()
    }
}
</script>

<style>
@import url('./assets/css/bootstrap.min.css');

body {
    font-family: Arial, "Microsoft YaHei"; 
}

.box {
    width: 400px;
    margin: 50px auto 0;
}

.puzzle-wrap {
    width: 400px;
    height: 400px;
    margin-bottom: 40px;
    padding: 0;
    background: #ccc;
    list-style: none;
}

.puzzle {
    float: left;
    width: 100px;
    height: 100px;
    font-size: 20px;
    background: #f90;
    text-align: center;
    line-height: 100px;
    border: 1px solid #ccc;
    box-shadow: 1px 1px 4px;
    text-shadow: 1px 1px 1px #B9B4B4;
    cursor: pointer;
}

.puzzle-empty {
    background: #ccc;
    box-shadow: inset 2px 2px 18px;
}

.btn-reset {
    box-shadow: inset 2px 2px 18px;
}
</style>