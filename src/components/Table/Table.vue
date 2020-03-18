<template>
	<div class="table" :style="`background-color: ${highlightedColor}`">
		<div v-if="tableData && tableData.rows && tableData.rows.length > 0">
			<div class="table">
				<div class="table-body" :style="{ height: height }">
					<!-- Table Body rows -->
					<div v-for="(tableRow, i) in tableData.rows" :key="i">
						<div
							class="table__row flex-c"
							:style="getRowStyle(tableRow.index)"
							>
							<!-- Table Body cells -->
							<div
								v-for="(tableCell, j) in tableRow.cells"
								:key="j"
								:class="`table__cell flex-c-s container--${tableCell.header.type}-header`"
								:style="getCellStyle(tableRow.index, j)"
								>
									<!-- Victim Header Cell -->
									<span
										:class="`table__cell-content ${ i !== 0 ? 'fill-width' : '' }`"
										:style="`white-space: ${whiteSpace}; overflow-wrap: ${wordWrap}; text-overflow: ${textOverflow};`"
										:id="tableCell.key"
										v-if="tableCell.header.type === 'victim'"
									>
										<b-container>
											<b-row>
												<b-col>
													<h5>Victim <span>({{ tableCell.header.index }} of {{ tableCell.header.total }})</span></h5>
													<p>Market To: {{ tableCell.header.marketTo ? 'Yes' : 'No' }}</p>
													<p>Web Account: {{ tableCell.header.webAccount ? 'Yes' : 'No' }}</p>
													<p v-for="field in tableCell.header.displayFields" :key="field.value" v-html="field.value"></p>
												</b-col>
												<b-col>
														<button class="btn --primary">Make Survivor</button>
												</b-col>
												<b-col class="icon__container">
													<i class="icon icon--history"></i>
													<i class="icon icon--trash"></i>
												</b-col>
											</b-row>
										</b-container>
									</span>

									<!-- Survivor Header Cell -->
									<span
										:class="`table__cell-content ${ i !== 0 ? 'fill-width' : '' }`"
										:style="`white-space: ${whiteSpace}; overflow-wrap: ${wordWrap}; text-overflow: ${textOverflow};`"
										:id="tableCell.key"
										v-if="tableCell.header.type === 'survivor'"
									>
										<b-container>
											<b-row>
												<b-col>
													<h5>Survivor</h5>
													<p>Market To: {{ tableCell.header.marketTo ? 'Yes' : 'No' }}</p>
													<p>Web Account: {{ tableCell.header.webAccount ? 'Yes' : 'No' }}</p>
													<p v-for="field in tableCell.header.displayFields" :key="field.value" v-html="field.value"></p>
												</b-col>
												<b-col class="icon__container">
													<i class="icon icon--history"></i>
													<i class="icon icon--trash"></i>
												</b-col>
											</b-row>
										</b-container>
									</span>

									<!-- Plain Data Cell -->
									<span
										:class="`table__cell-content ${ i !== 0 ? 'fill-width' : '' }`"
										:style="`white-space: ${whiteSpace}; overflow-wrap: ${wordWrap}; text-overflow: ${textOverflow}; ${tableCell.isHeading ? 'position: absolute; bottom: 0;' : ''}`"
										:id="tableCell.key"
										v-if="!tableCell.header"
									>
										<p v-html="tableCell.data"></p>
									</span>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import { populatedArray, is2DMatrix } from '../../utils/array.js';
import { generateUnique } from '../../utils/generateUnique.js';

// Statics
const wordWrapList = [ 'normal', 'break-word' ];
const whiteSpaceList = [ 'nowrap', 'normal', 'pre', 'pre-wrap', 'pre-line' ];
const textOverflowList = [ 'clip', 'ellipsis' ];

export default {
	name: 'table',
	data() {
		return {
			tableData           : {},
			searchValue         : '',
			activatedSort       : {},
			totalPages          : 0,
			pageSize            : 0,
		};
	},
	props: {
		/*
			params.data             : (Array) Tabular data
			params.highlight        : (Object) configure row / column / table cells for the highlighted background
			params.highlightedColor : (String) The color of the highlighted background.
			params.wordWrap         : (String) long word wrap for text in table cells 'normal / break-word' default normal
			params.whiteSpace       : (String) white space for text in table cells 'nowrap / normal / pre / pre-wrap / pre-line' default nowrap
			params.textOverflow     : (String) text overflow handling in table cells 'clip / ellipsis' default clip
			params.height           : (Number) table height.
			params.rowHeight        : (Number) table row height. Default 30
			params.columnWidth      : (Array) specifies the width of one or more columns, and the remaining column widths are evenly divided. [{column: 0, width: 80}, {column: 1, width: '20% '}]
			params.sort             : (Array) specifies sorting based on a column. Sort by specifying the first and second columns: [0, 1]. Only valid when the first row is configured as a table header
			params.edit             : (Object) configures editable row / column / table cells. For example: {row: [2, 3, ...], column: [3, 4, ...], cell: [[4, 4], [5, 6], ...]}; negative numbers indicate Reverse order (eg -1 is the last row / column); row: 'all' all rows
			Editing will change the data displayed in the table and will not change the incoming source data. When the component method is called to obtain the table data, the edited data is returned. The header is not editable. Disabled by default
			params.filter           : (Array) configure column-based filtering. For example: [{column: 0, content: [{text: '> 5', value: 5}], method: (value, tableCell) => {...}}]
			params.pageSize         : (Number) shows the number of pages per page
			params.pageSizes        : (Array) optional value for displaying the number of pages per page
		*/
		params: {
			type: Object,
			default: () => { return {}; }
		}
	},
	computed: {
		sourceData         () { return (Array.isArray(this.params.data)) ? this.params.data : [];                                                                             },
		highlightConfig    () { return (this.params.highlight && typeof this.params.highlight === 'object') ? this.params.highlight : {};                                     },
		cellStyle          () { return (this.params.cellStyle && typeof this.params.cellStyle === 'object') ? this.params.cellStyle : [];                                     },
		rowStyle           () { return (this.params.rowStyle && typeof this.params.rowStyle === 'object') ? this.params.rowStyle : [];                                        },
		highlightedColor   () { return (this.params.highlightedColor && typeof this.params.highlightedColor === 'string') ? this.params.highlightedColor : '#000000';         },
		wordWrap           () { return (this.params.wordWrap && wordWrapList.includes(this.params.wordWrap)) ? this.params.wordWrap : wordWrapList[0];                        },
		whiteSpace         () { return (this.params.whiteSpace && whiteSpaceList.includes(this.params.whiteSpace)) ? this.params.whiteSpace : whiteSpaceList[0];              },
		textOverflow       () { return (this.params.textOverflow && textOverflowList.includes(this.params.textOverflow)) ? this.params.textOverflow : textOverflowList[0];    },
		height             () { return (typeof this.params.height === 'number' && this.params.height > this.rowHeight) ? this.params.height - this.rowHeight + 'px' : 'auto'; },
		rowHeight          () { return (typeof this.params.rowHeight === 'number' && this.params.rowHeight >= 24) ? this.params.rowHeight : 30;                               },
		editConfig         () { return (this.params.edit && typeof this.params.edit === 'object') ? this.params.edit : {};                                                    },
		pageConfig         () { return (typeof this.params.pageSize === 'number' &&this.params.pageSize > 0) ? this.params.pageSize : 10;                                     },
		pageSizes          () { return (Array.isArray(this.params.pageSizes)) ? this.params.pageSizes : [10, 20, 50, 100];                                                    },
		columnWidth() {
			if (populatedArray(this.params.columnWidth)) {
				const obj = {};

				this.params.columnWidth.forEach(c => {
					if (c && typeof c.column === 'number' && c.column >= 0) {
						if (typeof c.width === 'number' && c.width >= 0) {
							obj[c.column] = c.width + 'px';
						} else if (typeof c.width === 'string' && /^(\d+\.?\d+?)%$/.test(c.width)) {
							obj[c.column] = c.width;
						}
					}
				});

				return obj;
			}

			return {};
		},
		filterConfig() {
			if (populatedArray(this.params.filter)) {
				const filterObj = {};

				this.params.filter.forEach(f => {
					if (
						f && typeof f.column === 'number'
						&& f.column >= 0
						&& typeof f.method === 'function'
						&& populatedArray(f.content)
					) {
						if (f.content.every(c => c && typeof c.text === 'string' && typeof c.value !== 'undefined')) {
							const content = f.content.map(c => new Object({ ...c, checked: false, key: generateUnique('content-') }));
							filterObj[f.column] = { ...f, content, key: generateUnique('filter-') };
						}
					}
				});

				return filterObj;
			}

			return {};
		},
	},
	watch: {
		params: {
			handler() {
				this.searchValue = '';
				this.activatedSort = {};
			},
			deep: true,
			immediate: true
		},
		sourceData: {
			handler() { this.initData(); },
			deep: true,
			immediate: true
		},
	},
	beforeDestroy() {
		this.tableData = {};
		this.activatedSort = {};
	},
	methods: {
		/**
		 * @function - Initialize Table data
		 */
		initData() {
			if (is2DMatrix(this.sourceData)) {
				const table = {
					key: generateUnique('table-'),
					rows: [],
					filteredRows: {}
				};

				for (let i = 0; i < this.sourceData.length; i++) {
					const tableRow = {
						key: generateUnique('table-'),
						show: true,
						filtered: false,
						inPage: false,
						index: i
					};

					tableRow.cells = this.sourceData[i].map(item => new Object({ data: item.html, isHeading: item.isHeading || false, header: item.header || false, key: generateUnique('table-'), checked: false }));
					table.rows.push(tableRow);
				}

				this.tableData = table;
			}
		},
		/**
		 * @function - Get style data of Cell
		 */
		getCellStyle(rowIndex, columnIndex) {
			const style = (this.cellStyle.length)
				? this.cellStyle.find(cur => {
					if ('column' in cur && 'row' in cur) {
						return cur.column == columnIndex && cur.row == rowIndex;
					} else if ('column' in cur) {
						return cur.column == columnIndex;
					} else if ('row' in cur) {
						return cur.row == rowIndex;
					}
				}) || {}
				: {};

			style.backgroundColor = this.isHighlighted(rowIndex, columnIndex) ? this.highlightedColor : '#ffffff';

			return (this.columnWidth[columnIndex]) ? {
				...style,
				flexGrow: 0,
				flexShrink: 0,
				flexBasis: this.columnWidth[columnIndex]
			} : {
				...style,
				flexGrow: 1,
				flexShrink: 1,
				flexBasis: '0%'
			};
		},
		/**
		 * @function - Get style data of Row
		 */
		getRowStyle(rowIndex) {
			const style = (this.rowStyle.length)
				? this.rowStyle.find(cur => cur.row === rowIndex) || {}
				: {};

			return {
				...style,
			};
		},
		/**
		 * @function - Get the latest data of the table. You can specify to include only the specified rows, or to include all data if you do not specify. Row order is initial order
		 * @param {Array} rowIndices - Specify the line. Such as：[ 0, 1, 2, ... ]
		 */
		getData(rowIndices) {
			const matrix = [];

			if (this.tableData && populatedArray(this.tableData.rows)) {
				const tmpRows = {};

				this.tableData.rows.forEach((row) => {
					(Array.isArray(rowIndices))
						? (rowIndices.includes(row.index)) ? tmpRows[row.index] = row : ''
						: tmpRows[row.index] = row;
				});

				for (let i = 0; i < this.tableData.rows.length; i++) {
					let rowData = this.getRowDataFromTableRow(tmpRows[i]);
					if (rowData.length > 0) matrix.push(rowData);
				}
			}

			return matrix;
		},
		/**
		 * @function - Get the latest data for the specified row based on the row index
		 * @param {Number} rowIndex - Row index
		 * @param {Boolean} isCurrent - Whether the index is a sorted index. Default false, that is, the original index
		 */
		getRowData(rowIndex, isCurrent = false) {
			if (this.tableData && populatedArray(this.tableData.rows)) {
				const row = (isCurrent) ? this.tableData.rows[rowIndex] : this.tableData.rows.find(r => r.index === rowIndex);
				return this.getRowDataFromTableRow(row);
			}

			return [];
		},
		/**
		 * @function - Get the latest data of the specified Cell according to the row and column indices
		 * @param {Number} rowIndex - Row index
		 * @param {Number} columnIndex - Column index
		 * @param {Boolean} isCurrent - Whether the row index is a sorted index. Default false, that is, the original index
		 */
		getCellData(rowIndex, columnIndex, isCurrent = false) {
			if (this.tableData && populatedArray(this.tableData.rows)) {
				const row = (isCurrent) ? this.tableData.rows[rowIndex] : this.tableData.rows.find(r => r.index === rowIndex);
				if (!(row && populatedArray(row.cells))) return '';

				const cell = row.cells[columnIndex];
				if (cell && typeof cell.data !== 'undefined') return cell.data;

				return '';
			}

			return '';
		},
		isHighlighted(rowIndex, columnIndex) {
			if (
				!this.highlightConfig
				|| (this.highlightConfig.row && this.highlightConfig.column && this.highlightConfig.cell)
			) return false;

			if (
				Array.isArray(this.highlightConfig.row)
				&& (this.highlightConfig.row.includes(rowIndex)
				|| this.highlightConfig.row.includes(rowIndex - this.sourceData.length))
			) return true;

			if (
				Array.isArray(this.highlightConfig.column)
				&& (this.highlightConfig.column.includes(columnIndex) || this.highlightConfig.column.includes(columnIndex - this.sourceData[0].length))
			) return true;

			if (
				Array.isArray(this.highlightConfig.cell) &&
				this.highlightConfig.cell.length > 0
			) {
				return this.highlightConfig.cell.some(item => (
					Array.isArray(item)
					&& item.length >= 2
					&& (item[0] === rowIndex || item[0] === rowIndex - this.sourceData.length)
					&& (item[1] === columnIndex || item[1] === columnIndex - this.sourceData[0].length)
				));
			}

			return false;
		}
	}
};
</script>

<style lang='scss' scoped>
@import './Table.scss';
</style>