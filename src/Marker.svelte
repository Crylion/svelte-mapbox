<script>
  import { getContext, onMount, createEventDispatcher } from "svelte";
  import { contextKey } from "./mapbox.js";

  const { getMap, getMapbox } = getContext(contextKey);
  const map = getMap();
  const mapbox = getMapbox();
  const dispatch = createEventDispatcher();

  export let lat;
  export let lon;
  /**
   * Object with following optional properties:
   * label - text of the popup
   * offset - pixel offset of popup
   **/
  export let popup;

  // reference to html in user defined slot
  let referenceToSlot;

  onMount(() => {
	// if the user added custom html to the marker via the slot, we add a click handler to it
	// to provide a markerClick event
	if (referenceToSlot.firstElementChild !== null) {
      referenceToSlot.firstElementChild.addEventListener("click", () => dispatch('markerClick'));
    }

	// also use the custom html as the element for the marker if available
    const marker =
      referenceToSlot.firstElementChild !== null
        ? new mapbox.Marker({ element: referenceToSlot.firstElementChild })
        : new mapbox.Marker();

    marker.setLngLat([lon, lat]);

    if (popup !== undefined) {
      const popup = new mapbox.Popup({
        offset: popup.offset !== undefined ? popup.offset : 25
      }).setText(popup.label);
      marker.setPopup(popup);
    }

    marker.addTo(map);
  });
</script>

<div bind:this={referenceToSlot}>
  <slot />
</div>
