<template>
	<component
		:is="tag"
		:style="computedStyle"
		@pointerenter="isHover = true"
		@pointerleave="isHover = false; isActive = false"
		@pointerdown="isActive = true"
		@pointerup="isActive = false"
		@pointercancel="isActive = false"
		@focus="isFocus = true"
		@blur="isFocus = false"
		@focusin="isFocusWithin = true"
		@focusout="isFocusWithin = false"
	>
		<slot />
	</component>
</template>
<script>
	/**
	 * Block - A responsive, themeable container component.
	 *
	 * @component
	 * @example
	 * <!-- Basic usage -->
	 * <Block adapt="padding: 16px; background: #fff">
	 *   Content here
	 * </Block>
	 *
	 * @example
	 * <!-- Responsive styles with breakpoints -->
	 * <Block adapt="padding: 8px; md: padding: 16px; lg: padding: 24px">
	 *   Responsive padding
	 * </Block>
	 *
	 * @example
	 * <!-- With state-based styles -->
	 * <Block adapt="background: white; hover: background: gray">
	 *   Hover me
	 * </Block>
	 *
	 * @example
	 * <!-- With theme support -->
	 * <Block adapt="dark: background: #333; light: background: #fff">
	 *   Themed content
	 * </Block>
	 *
	 * @example
	 * <!-- Object syntax -->
	 * <Block :adapt="{ padding: '16px', 'md': 'padding: 24px' }">
	 *   Object syntax
	 * </Block>
	 *
	 * @example
	 * <!-- Array syntax -->
	 * <Block :adapt="['padding: 16px', { hover: 'background: gray' }]">
	 *   Array syntax
	 * </Block>
	 *
	 * @props
	 * - tag: HTML tag to render (default: 'div')
	 * - adapt: Responsive/themeable styles as string, object, or array
	 * - state: Custom states to apply (string or array, e.g., 'selected' or ['selected', 'expanded'])
	 * - disabled: Adds 'disabled' state
	 * - themeName: Override injected theme
	 * - breakpointName: Override injected breakpoint
	 * - breakpointStrategy: 'mobile-first' | 'desktop-first' | 'exact' (default: 'mobile-first')
	 * - themeStrategy: 'fallback' | 'strict' (default: 'fallback')
	 */
	import {
		parse,
		getStyle
	} from '@componentor/adaptive';
	export default {
		wysiwyg: true,
		inject: {
			theme: {
				default: ''
			},
			breakpoint: {
				default: ''
			}
		},
		data() {
			return {
				isHover: false,
				isActive: false,
				isFocus: false,
				isFocusWithin: false
			};
		},
		props: {
			tag: {
				type: String,
				default: 'div'
			},
			adapt: {
				type: [String, Object, Array],
				default: ''
			},
			state: {
				type: [String, Array],
				default: ''
			},
			disabled: {
				type: Boolean,
				default: false
			},
			themeName: {
				type: String,
				default: ''
			},
			breakpointName: {
				type: String,
				default: ''
			},
			breakpointStrategy: {
				type: String,
				default: 'mobile-first',
				validator: v => ['exact', 'mobile-first', 'desktop-first'].includes(v)
			},
			themeStrategy: {
				type: String,
				default: 'fallback',
				validator: v => ['strict', 'fallback'].includes(v)
			}
		},
		computed: {
			adaptString() {
				if (!this.adapt) return '';
				if (typeof this.adapt === 'string') return this.adapt;
				if (Array.isArray(this.adapt)) {
					return this.adapt.map(item => {
							if (typeof item === 'string') return item;
							return Object.entries(item)
								.map(([key, value]) => `${key}:${value}`)
								.join('; ');
						})
						.join('; ');
				}
				return Object.entries(this.adapt)
					.map(([key, value]) => `${key}:${value}`)
					.join('; ');
			},
			stateArray() {
				const native = [];
				if (this.isHover) native.push('hover');
				if (this.isActive) native.push('active');
				if (this.isFocus) native.push('focus');
				if (this.isFocusWithin) native.push('focus-within');
				if (this.disabled) native.push('disabled');
				if (!this.state) return native;
				const custom = Array.isArray(this.state) ? this.state : this.state.split(':');
				return [...native, ...custom];
			},
			computedStyle() {
				if (!this.adaptString) return '';
				const parsed = parse(this.adaptString);
				return getStyle(parsed, {
					theme: this.themeName || this.theme,
					breakpoint: this.breakpointName || this.breakpoint,
					states: this.stateArray,
					breakpointStrategy: this.breakpointStrategy,
					themeStrategy: this.themeStrategy
				});
			}
		}
	};

</script>
<style scoped></style>