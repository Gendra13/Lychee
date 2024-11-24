<template>
	<Panel id="lychee_view_content" :header="$t(props.header)" class="w-full border-0">
		<template #icons>
			<a class="px-1 cursor-pointer group" @click="(layout = 'square') && activateLayout()" :title="$t('lychee.LAYOUT_SQUARES')">
				<MiniIcon icon="squares" fill="fill-transparent" :class="squareClass" />
			</a>
			<a class="px-1 cursor-pointer group" @click="(layout = 'justified') && activateLayout()" :title="$t('lychee.LAYOUT_JUSTIFIED')">
				<MiniIcon icon="justified" fill="" :class="justifiedClass" />
			</a>
			<a class="px-1 cursor-pointer group" @click="(layout = 'masonry') && activateLayout()" :title="$t('lychee.LAYOUT_MASONRY')">
				<MiniIcon icon="masonry" fill="fill-transparent" :class="masonryClass" />
			</a>
			<a class="px-1 cursor-pointer group" @click="(layout = 'grid') && activateLayout()" :title="$t('lychee.LAYOUT_GRID')">
				<MiniIcon icon="grid" fill="fill-transparent" :class="gridClass" />
			</a>
		</template>
		<div class="relative flex flex-wrap flex-row flex-shrink w-full justify-start align-top" id="photoListing">
			<template v-for="(photo, idx) in props.photos">
				<PhotoThumb
					@click="maySelect(idx, $event)"
					@contextmenu.prevent="menuOpen(idx, $event)"
					:is-selected="props.selectedPhotos.includes(photo.id)"
					:photo="photo"
					:album="props.album"
					:is-lazy="idx > 10"
				/>
			</template>
		</div>
	</Panel>
</template>
<script setup lang="ts">
import { onMounted, onUpdated } from "vue";
import Panel from "primevue/panel";
import PhotoThumb from "@/components/gallery/thumbs/PhotoThumb.vue";
import MiniIcon from "@/components/icons/MiniIcon.vue";
import { useLayouts } from "@/layouts/PhotoLayout";

const props = defineProps<{
	header: string;
	photos: { [key: number]: App.Http.Resources.Models.PhotoResource };
	photoLayout: App.Enum.PhotoLayoutType;
	album:
		| App.Http.Resources.Models.AlbumResource
		| App.Http.Resources.Models.TagAlbumResource
		| App.Http.Resources.Models.SmartAlbumResource
		| undefined;
	galleryConfig: App.Http.Resources.GalleryConfigs.PhotoLayoutConfig;
	selectedPhotos: string[];
}>();

// bubble up.
const emits = defineEmits<{
	clicked: [idx: number, event: MouseEvent];
	contexted: [idx: number, event: MouseEvent];
}>();
const maySelect = (idx: number, e: MouseEvent) => emits("clicked", idx, e);
const menuOpen = (idx: number, e: MouseEvent) => emits("contexted", idx, e);

// Layouts stuff
const { activateLayout, layout, squareClass, justifiedClass, masonryClass, gridClass } = useLayouts(props.galleryConfig, props.photoLayout);
onMounted(() => activateLayout());
onUpdated(() => activateLayout());
</script>
