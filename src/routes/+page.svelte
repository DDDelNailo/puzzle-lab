<script lang="ts">
    function hasDuplicate(arr: number[]): boolean {
        const seen = new Set<number>();

        for (const num of arr) {
            if (num === 0) {
                continue;
            }

            if (seen.has(num)) {
                return true;
            }

            seen.add(num);
        }

        return false;
    }

    function isValidRow(board: number[][], posY: number): boolean {
        const row = board[posY];

        return !hasDuplicate(row);
    }

    function isValidCol(board: number[][], posX: number): boolean {
        const col: number[] = board.map((row) => row[posX]);

        return !hasDuplicate(col);
    }

    function isValidBox(
        board: number[][],
        posY: number,
        posX: number,
    ): boolean {
        const startRow = Math.floor(posY / 3) * 3;
        const startCol = Math.floor(posX / 3) * 3;

        const endRow = startRow + 2;
        const endCol = startCol + 2;

        const nums: number[] = [];

        for (let posY = startRow; posY <= endRow; posY++) {
            for (let posX = startCol; posX <= endCol; posX++) {
                nums.push(board[posY][posX]);
            }
        }

        return !hasDuplicate(nums);
    }

    function isValidMove(
        board: number[][],
        posX: number,
        posY: number,
        num: number,
    ): boolean {
        const newBoard: number[][] = structuredClone(board);
        newBoard[posY][posX] = num;

        return (
            isValidRow(newBoard, posY) &&
            isValidCol(newBoard, posX) &&
            isValidBox(newBoard, posY, posX)
        );
    }

    function isValidBoard(board: number[][]): boolean {
        for (let posX = 0; posX < 9; posX++) {
            if (!isValidCol(board, posX)) return false;
        }

        for (let posY = 0; posY < 9; posY++) {
            if (!isValidRow(board, posY)) return false;
        }

        for (let posY = 0; posY < 3; posY++) {
            for (let posX = 0; posX < 3; posX++) {
                if (!isValidBox(board, posY * 3, posX * 3)) return false;
            }
        }

        return true;
    }

    function isComplete(board: number[][]): boolean {
        return !board.flat().includes(0) && isValidBoard(board);
    }

    function isGiven(posX: number, posY: number): boolean {
        return startBoard[posY][posX] !== 0;
    }

    function canSetCell(posX: number, posY: number, num: number): boolean {
        if (num < 0 || num > 9) return false;
        if (isGiven(posX, posY)) return false;
        if (!isValidMove(currBoard, posX, posY, num)) return false;

        return true;
    }

    function triggerError(x: number, y: number): void {
        clearTimeout(errorTimeout);
        errorX = x;
        errorY = y;

        errorTimeout = setTimeout(() => {
            errorX = null;
            errorY = null;
        }, 300);
    }

    function winCheck(): void {
        if (isComplete(currBoard)) {
            isWon = true;
        }
    }

    function resetGame(): void {
        currBoard = structuredClone(startBoard);
        isWon = false;
    }

    function handleKeydown(event: KeyboardEvent): void {
        if (selectX === null || selectY === null) return;
        if (isGiven(selectX, selectY)) return;

        const key = event.key;

        if (/^[1-9]$/.test(key)) {
            const num = Number(key);

            if (!canSetCell(selectX, selectY, num)) {
                triggerError(selectX, selectY);
                return;
            }

            currBoard[selectY][selectX] = num;
        } else if (key === "Backspace" || key === "Delete") {
            currBoard[selectY][selectX] = 0;
        }

        if (!isWon) {
            winCheck();
        }
    }

    const puzzle = [
        [5, 3, 0, 0, 7, 8, 9, 0, 2],
        [6, 0, 2, 1, 9, 0, 3, 4, 8],
        [1, 9, 8, 0, 4, 2, 0, 6, 0],
        [8, 5, 0, 7, 0, 1, 4, 0, 3],
        [4, 0, 6, 8, 5, 3, 7, 9, 0],
        [7, 1, 3, 0, 2, 0, 8, 5, 6],
        [0, 6, 0, 5, 3, 7, 2, 8, 4],
        [2, 8, 7, 0, 1, 9, 0, 3, 5],
        [3, 0, 5, 2, 8, 0, 1, 7, 0],
    ];

    const startBoard: number[][] = structuredClone(puzzle);
    let currBoard: number[][] = structuredClone(startBoard);

    let selectX: number | null = null;
    let selectY: number | null = null;

    let errorX: number | null = null;
    let errorY: number | null = null;
    let errorTimeout: ReturnType<typeof setTimeout>;

    let isWon: boolean = false;
</script>

<svelte:window on:keydown={handleKeydown} />

<main>
    <table class:won={isWon}>
        <tbody>
            {#each currBoard as row, rowIndex}
                <tr
                    class:box-bottom={(rowIndex + 1) % 3 === 0 &&
                        rowIndex !== 8}
                >
                    {#each row as cell, colIndex}
                        <td
                            class:box-right={(colIndex + 1) % 3 === 0 &&
                                colIndex !== 8}
                            class:given={isGiven(colIndex, rowIndex)}
                            class:selected={selectX === colIndex &&
                                selectY === rowIndex}
                            class:error={errorX === colIndex &&
                                errorY === rowIndex}
                        >
                            <button
                                on:click={() => {
                                    selectX = colIndex;
                                    selectY = rowIndex;
                                }}
                            >
                                {cell || ""}
                            </button>
                        </td>
                    {/each}
                </tr>
            {/each}
        </tbody>
    </table>
    <button on:click={resetGame}> Reset </button>
</main>

<style>
    main {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        background-color: #0d0e15;
        font-family:
            system-ui,
            -apple-system,
            BlinkMacSystemFont,
            "Segoe UI",
            Roboto,
            sans-serif;
    }

    main > button {
        margin-top: 1rem;
        padding: 0.5rem 1rem;
        font-size: 1rem;
        font-weight: 600;
        color: #f8fafc;
        background-color: #61308f;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        transition: all 0.15s ease-in-out;

        &:hover {
            background-color: #9440e3;
            box-shadow: 0 0 12px rgba(114, 52, 172, 0.6);
        }
    }

    table {
        border-collapse: collapse;
    }

    td {
        width: 52px;
        height: 52px;
        text-align: center;
        vertical-align: middle;
        font-size: 1.5rem;
        font-weight: 600;
        color: #f8fafc;
        border: 1px solid #2d3148;
        transition: all 0.15s ease-in-out;

        &.given {
            color: #9440e3;
        }

        &:hover {
            background-color: rgba(114, 52, 172, 0.2);
            box-shadow: inset 0 0 12px rgba(106, 51, 158, 0.4);
        }

        &.selected {
            background-color: rgba(114, 52, 172, 0.4);
            box-shadow: inset 0 0 12px rgba(106, 51, 158, 0.6);
        }
    }

    td > button {
        width: 100%;
        height: 100%;
        background: none;
        border: none;
        color: inherit;
        font: inherit;
        cursor: pointer;
    }

    tr.box-bottom td {
        border-bottom: 3px solid #61308f;
    }

    td.box-right {
        border-right: 3px solid #61308f;
    }

    td.error {
        background-color: rgba(239, 68, 68, 0.25) !important;
        box-shadow: inset 0 0 12px rgba(239, 68, 68, 0.6) !important;
        border-color: #ef4444 !important;
        animation: shake 0.3s ease-in-out;
    }

    @keyframes shake {
        0%,
        100% {
            transform: translateX(0);
        }
        20% {
            transform: translateX(-4px);
        }
        40% {
            transform: translateX(4px);
        }
        60% {
            transform: translateX(-3px);
        }
        80% {
            transform: translateX(3px);
        }
    }

    .won {
        border-color: #a855f7;
        animation: winPulse 1.5s infinite alternate;
    }

    @keyframes winPulse {
        from {
            box-shadow: 0 0 20px rgba(168, 85, 247, 0.3);
        }
        to {
            box-shadow: 0 0 40px rgba(168, 85, 247, 0.8);
        }
    }
</style>
