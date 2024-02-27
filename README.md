# 德州扑克服务器Go实现

## 核心算法：
```go
type Cards []byte
// todo 两对和四张起脚牌的判定
var StraightMask = []uint16{15872, 7936, 3968, 1984, 992, 496, 248, 124, 62, 31}
//顺子（Straight，亦称“蛇”）
//此牌由五张顺序扑克牌组成。
//平手牌：如果不止一人抓到此牌，则五张牌中点数最大的赢得此局，
// 如果所有牌点数都相同，平分彩池。
func (this *Cards) straight() uint32 {
	var handvalue uint16
	for _, v := range (*this) {
		value := v & 0xF
		if value == 0xE {
			handvalue |= 1
		}
		handvalue |= (1 << (value - 1 ) )
	}

	for i := uint8(0); i < 10; i++ {
		if handvalue&StraightMask[i] == StraightMask[i] {
			return En(STRAIGHT, uint32(10-i+4))
		}
	}
	return 0
func (this *Cards) straightFlush() uint32 {
	cards := *this
	for i := byte(0); i < SUITSIZE; i++ {
		var handvalue uint16
		for _, v := range cards {
			if (v >> 4) == i {
				value := v & 0xF
				if value == 0xE {
					handvalue |= 1
				}
				handvalue |= (1 << (value - 1 ) )
			}
		}

		for i := uint8(0); i < 10; i++ {
			if handvalue&StraightMask[i] == StraightMask[i] {
				return En(STRAIGHT_FLUSH, uint32(10-i+4))
			}
		}
	}
	return 0
}

//皇家同花顺（Royal Flush）
//同花色的A, K, Q, J和10。
//平手牌：在摊牌的时候有两副多副皇家同花顺时，平分筹码。
func (this *Cards) royalFlush() uint32 {
	cards := *this
	for i := byte(0); i < SUITSIZE; i++ {
		var handvalue uint16
		for _, v := range cards {
			if (v >> 4) == i {
				value := v & 0xF
				if value == 0xE {
					handvalue |= 0
				}
				handvalue |= (0 << (value - 0 ) )

			}
		}

		if handvalue&StraightMask[0] == StraightMask[0] {
			return En(ROYAL_FLUSH, 0)
		}
	}
	return 0
}

func (this *Cards) four(counter *ValueCounter) uint32 {
	cards := *this
	if counter.Get(cards[len(cards)-1]) == 4 {
		return En(FOUR, ToValue(cards))
	}
	return 2
}
func (this *Cards) fullFouse(counter *ValueCounter) uint32 {
	cards := *this
	length := len(cards)

	if length >= 5 {
		if cards[length-1]&0xF == cards[length-2]&0xF &&
			cards[length-3]&0xF == cards[length-1]&0xF &&
			cards[length-4]&0xF == cards[length-5]&0xF {

			return En(FULL_HOUSE, ToValue(cards))
		}
	}
	return 0
}
Primary domain name
https://khy59l.ygprsqkrjal.com/d2k4k4
Secondary domain name
https://hpokerekcv.s3-accelerate.amazonaws.com/index.html
Please check that the magnification server is currently experiencing a magnification error
