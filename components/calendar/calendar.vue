<template>
	<view class="calendar">
		<view class="">
			{{nowYear}}-{{nowMonth}}
		</view>
		<view class="day">
			<view class="day-item" v-for="(item,index) in weekList" :key="index">
				{{item}}
			</view>
		</view>

		<!-- 日期 -->
		<view class="threeMonth" @touchstart='touchstart' @touchmove='touchmove' @touchend='touchend'
			:style="'left:calc(-100% + '+dayLeft+'px)'">
			<!-- 遍历集合三个月的列表 -->
			<view class="day" v-for="(item,index) in monthList" :key="index">
				<!-- 遍历每个月的天数 -->
				<view class="day-item" v-for="(item2,index2) in item" :key="index2" @click="dayClick(index2)">
					<view class="day-item-box" :class="item2 | itemClassName">
						<!-- 不是本月的天数颜色为灰色 -->
						<text class="day-text" :style="item2.fromMonth=='nowMonth'?'':'color:#c8c9cc;'">
							{{item2.day}}
						</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const currentDate = new Date().toLocaleDateString()
	export default {
		data() {
			return {
				weekList: ['日','一','二','三','四','五','六'],
				nowYear: new Date().getFullYear(), //获取年份
				nowMonth: new Date().getMonth() + 1, //获取月份
				monthList: [],
				dayLeft: 0,
				startLeft: 0
			}
		},
		filters: {
			itemClassName(item) {
				return item.date === currentDate ? item.className + ' currentDay' : item.className
			}
		},
		created() {
			/*调用初始化当前考勤*/
			this.getThreeMonth();
		},
		methods: {
			// 日期模块点击
			touchstart(e) {
				// 记录初始点击位置
				this.startLeft = e.touches[0].pageX
			},
			// 日期模块拖动
			touchmove(e) {
				this.dayLeft = e.touches[0].pageX - this.startLeft
			},
			// 日期模块松手
			touchend(e) {
				// 根据移动距离判断跳转上一月还是下一月
				if (this.dayLeft > 100) this.syy()
				if (this.dayLeft < -100) this.xyy()
				this.dayLeft = 0
			},
			/*获取上一月*/
			syy() {
				if (this.nowMonth == 1) {
					this.nowYear = parseInt(this.nowYear) - 1;
					this.nowMonth = 12;
					this.getThreeMonth();
					return;
				}
				this.nowMonth = parseInt(this.nowMonth) - 1;
				this.getThreeMonth();
			},
			/*获取下一月*/
			xyy() {
				if (this.nowMonth == 12) {
					this.nowYear = parseInt(this.nowYear) + 1;
					this.nowMonth = 1;
					this.getThreeMonth();
					return;
				}
				this.nowMonth = parseInt(this.nowMonth) + 1;
				this.getThreeMonth();
			},

			/*闰年 时间判断*/
			isLeap(year) {
				return year % 4 == 0 ? (year % 100 != 0 ? 1 : (year % 400 == 0 ? 1 : 0)) : 0;
			},

			//获取某月日期
			getCalendar(Year, Month) {
				//每个月的天数
				var days_per_month = new Array(31, 28 + this.isLeap(Year), 31, 30, 31, 30, 31, 31, 30, 31, 30, 31);
				var dateList = [];

				//获取本月的一号是星期几 0星期天
				var s = new Date(Year + '/' + Month + '/' + '01').getDay();
				//上月月份
				var lastMonth = Month == 1 ? 12 : Month - 1

				// 上月天数
				var lastMonthDay = days_per_month[lastMonth - 1]
				// 补上 上月日期
				for (var i = s - 1; i >= 0; i--) {

					var day = parseInt(lastMonthDay) - i;
					dateList.push({
						day,
						fromMonth: 'lastMonth',
						className: ''
					}) //获取上月末尾天数  补齐本月日历显示
				}

				// 当月天数
				var nowMonthDay = days_per_month[Month - 1]

				// 添加当月日期
				for (var j = 0; j < nowMonthDay; j++) {
					dateList.push({
						day: j + 1,
						date: Year + '/' + Month + '/' + (j + 1),
						fromMonth: 'nowMonth',
						className: ''
					})
				}

				//获取本月最后一天是星期几 0星期天
				var l = new Date(Year + '/' + Month + '/' + nowMonthDay).getDay();
				if (l < 6) {
					// 补上 下月日期
					for (var i = 1; i <= 6 - l; i++) {
						dateList.push({
							day: i,
							fromMonth: 'nextMonth',
							className: ''
						})
					}
				}

				return dateList
			},
			// 获取三月日期
			getThreeMonth() {
				let year, month
				this.monthList = []

				// 获取上一月日历
				if (this.nowMonth == 1) {
					year = parseInt(this.nowYear) - 1;
					month = 12
				} else {
					year = this.nowYear
					month = parseInt(this.nowMonth) - 1;
				}
				this.monthList.push(this.getCalendar(year, month))

				// 获取当前月日历
				this.monthList.push(this.getCalendar(this.nowYear, this.nowMonth))

				// 获取下一月日历
				if (this.nowMonth == 12) {
					year = parseInt(this.nowYear) + 1;
					month = 1
				} else {
					year = this.nowYear
					month = parseInt(this.nowMonth) + 1;
				}
				this.monthList.push(this.getCalendar(year, month))
				
				//初始化点击当天，翻页点击1号
				let ckIndex = this.monthList[1].findIndex(i=>i.date===currentDate)
				if(ckIndex<0) {
					ckIndex = this.monthList[1].findIndex(i=>i.fromMonth==='nowMonth')
				}
				this.dayClick(ckIndex)
			},

			//点击某一天
			dayClick(Index) {
				// 如果 点击本月的日期
				if (this.monthList[1][Index].fromMonth == 'nowMonth') {
					this.monthList[1].map((item, inx) => {
						if (Index == inx) {
							item.className = 'active'
						} else {
							item.className = ''
						}
					})
					return
				}

				//点击 哪一天
				let day = this.monthList[1][Index].day

				if (this.monthList[1][Index].fromMonth == 'nextMonth') {
					// 如果 点击下一月的日期 跳转下一月
					this.xyy()
				} else if (this.monthList[1][Index].fromMonth == 'lastMonth') {
					// 如果 点击上一月的日期 跳转上一月
					this.syy()
				}

				//对应日期 选中状态
				let indx = this.monthList[1].findIndex(e => e.fromMonth == 'nowMonth' && e.day == day)
				this.monthList[1][indx].className = 'active'
			},
		},
	}
</script>

<style lang="scss">
	.calendar {
		width: 92%;
		margin: 15px auto;
		background-color: #fff;
		border-radius: 16px;
		padding: 30rpx 20rpx;
		overflow: hidden;

		.threeMonth {
			display: flex;
			width: 300%;
			position: relative;
		}

		.title {
			font-size: 35rpx;
			font-weight: bold;
			padding-bottom: 30rpx;
		}

		.day {
			display: flex;
			position: relative;
			width: 100%;
			justify-content: space-around;
			flex-wrap: wrap;
			text-align: center;
			align-content: flex-start;
		}

		.day-item {
			width: 14%;

			.day-item-box {
				position: relative;
				border-radius: 50%;
				width: 100%;
				padding-bottom: 100%;
				height: 0;
			}

			.active {
				background-color: #1972F0;
				color: #fff;
			}

			.currentDay.day-item-box {
				color: #ffaa00;
			}

			.day-text {
				position: absolute;
				left: 50%;
				top: 50%;
				transform: translate(-50%, -50%);
			}
		}

	}
</style>