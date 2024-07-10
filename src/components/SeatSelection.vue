<template>
    <div>
        <p>請選擇觀看電影的座位</p>
        <div class="seat-selection">
            <div class="legend">
                <div class="legend-item">
                    <div class="seat selected"></div>
                    <span>您的座位</span>
                </div>
                <div class="legend-item">
                    <div class="seat available"></div>
                    <span>未售出</span>
                </div>
                <div class="legend-item">
                    <div class="seat sold"></div>
                    <span>已售出</span>
                </div>
            </div>
            <div class="screen">螢幕screen</div>
            <div class="seats">
                <div
                    v-for="(row, rowIndex) in seats"
                    :key="rowIndex"
                    class="seat-row"
                >
                    <div
                        v-for="(seat, seatIndex) in row"
                        :key="seat.key"
                        :class="[
                            'seat',
                            seat.status,
                            {
                                selected: seat.key === selectedSeatKey,
                                'mr50': (seatIndex + 1) === 3,
                                'ml50': (seatIndex + 1) === 14
                            }
                        ]"
                        @click="selectSeat(rowIndex, seatIndex)"
                    ></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, computed } from 'vue'

interface Seat {
    key: string;
    status: 'available' | 'sold' | 'selected';
}

/**
 * 配置對象
 * @type {Object}
 */
const CONFIG = {
    /** 座位行標識 */
    ROWS: 'ABCDEFGHIJ',
    /** 每行座位數 */
    SEATS_PER_ROW: 16,
    /** 座位被售出的概率 */
    SOLD_PROBABILITY: 0.3
};

/**
 * 座位陣列
 * @type {Seat[][]}
 */
const seats = reactive<Seat[][]>([]);

/**
 * 當前選中的座位
 * @type {Ref<{row: number, seat: number} | null>}
 */
const selectedSeat = ref<{ row: number, seat: number } | null>(null);

/**
 * 產生座位數據
 * @returns {Seat[][]} 二維座位陣列
 */
const generateSeats = (): Seat[][] => {
    return CONFIG.ROWS.split('').map(row =>
        Array.from({ length: CONFIG.SEATS_PER_ROW }, (_, index) => ({
            key: `${row}${index + 1}`,
            status: Math.random() < CONFIG.SOLD_PROBABILITY ? 'sold' : 'available'
        }))
    );
};

/**
 * 計算屬性: 當前選中座位的鍵值
 * @type {ComputedRef<string | null>}
 */
const selectedSeatKey = computed(() =>
    selectedSeat.value
        ? seats[selectedSeat.value.row][selectedSeat.value.seat].key
        : null
);

/**
 * 選擇座位
 * @param {number} row - 行索引
 * @param {number} seat - 座位索引
 */
const selectSeat = (row: number, seat: number) => {
    if (seats[row][seat].status === 'available') {
        selectedSeat.value = { row, seat };
    }
};

onMounted(() => {
    seats.push(...generateSeats());
});
</script>

<style scoped>
.seat-selection {
    width: 794px;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 48px;
}

.legend {
    width: 276px;
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
}

.legend-item {
    display: flex;
    align-items: center;
}

.legend-item span {
    font-size: 12px;
}

.seat {
    width: var(--seat-size);
    height: var(--seat-size);
    margin: var(--seat-margin);
    cursor: pointer;
    border: 2px solid var(--seat-border-color);
    border-radius: 50%;
}

.sold {
    background: var(--seat-border-color);
    border: none;
}

.selected {
    background-color: var(--seat-selected-color);
    border: none;
}

.screen {
    width: 472px;
    padding: 4px 9px;
    text-align: center;
    border: 1px solid var(--screen-border-color);
    margin-top: 32px;
    margin-bottom: 32px;
}

.seats {
    display: flex;
    flex-direction: column;
}

.seat-row {
    display: flex;
    justify-content: center;
}

.mr50 {
    margin-right: 20px;
}

.ml50 {
    margin-left: 20px;
}
</style>