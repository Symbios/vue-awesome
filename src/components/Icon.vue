<script>
"use strict";

function assign(obj) {
  for (var _len = arguments.length, sources = Array(_len > 1 ? _len - 1 : 0), _key = 1; _key < _len; _key++) {
    sources[_key - 1] = arguments[_key];
  }

  sources.forEach(function (source) {
    for (var key in source) {
      if (source.hasOwnProperty(key)) {
        obj[key] = source[key];
      }
    }
  });

  return obj;
}

Object.defineProperty(exports, "__esModule", {
  value: true
});

function _defineProperty(obj, key, value) { if (key in obj) { Object.defineProperty(obj, key, { value: value, enumerable: true, configurable: true, writable: true }); } else { obj[key] = value; } return obj; }

function _toConsumableArray(arr) { if (Array.isArray(arr)) { for (var i = 0, arr2 = Array(arr.length); i < arr.length; i++) { arr2[i] = arr[i]; } return arr2; } else { return Array.from(arr); } }

var icons = {};

exports.default = {
  name: 'fa-icon',
  render: function render(h) {
    if (this.name === null) {
      return h();
    }

    var options = {
      class: this.klass,
      style: this.style,
      attrs: {
        role: this.label ? 'img' : 'presentation',
        'aria-label': this.label || null,
        x: this.x,
        y: this.y,
        width: this.width,
        height: this.height,
        viewBox: this.box
      }
    };

    if (this.raw) {
      options.domProps = {
        innerHTML: this.raw
      };
    }

    return h('svg', options, this.raw && this.icon ? null : this.$slots.default || [].concat(_toConsumableArray(this.icon.paths.map(function (path, i) {
      return h('path', {
        attrs: path,
        key: 'path-' + i
      });
    })), _toConsumableArray(this.icon.polygons.map(function (polygon, i) {
      return h('polygon', {
        attrs: polygon,
        key: 'polygon-' + i
      });
    }))));
  },

  props: {
    name: {
      type: String,
      validator: function validator(val) {
        if (val && !(val in icons)) {
          console.warn('Invalid prop: prop "name" is referring to an unregistered icon "' + val + '".' + '\nPlease make sure you have imported this icon before using it.');
          return false;
        }
        return true;
      }
    },
    scale: [Number, String],
    spin: Boolean,
    inverse: Boolean,
    pulse: Boolean,
    flip: {
      validator: function validator(val) {
        return val === 'horizontal' || val === 'vertical';
      }
    },
    label: String
  },
  data: function data() {
    return {
      x: false,
      y: false,
      childrenWidth: 0,
      childrenHeight: 0,
      outerScale: 1
    };
  },

  computed: {
    normalizedScale: function normalizedScale() {
      var scale = this.scale;
      scale = typeof scale === 'undefined' ? 1 : Number(scale);
      if (isNaN(scale) || scale <= 0) {
        console.warn('Invalid prop: prop "scale" should be a number over 0.', this);
        return this.outerScale;
      }
      return scale * this.outerScale;
    },
    klass: function klass() {
      return _defineProperty({
        'fa-icon': true,
        'fa-spin': this.spin,
        'fa-flip-horizontal': this.flip === 'horizontal',
        'fa-flip-vertical': this.flip === 'vertical',
        'fa-inverse': this.inverse,
        'fa-pulse': this.pulse
      }, this.$options.name, true);
    },
    icon: function icon() {
      if (this.name) {
        return icons[this.name];
      }
      return null;
    },
    box: function box() {
      if (this.icon) {
        return '0 0 ' + this.icon.width + ' ' + this.icon.height;
      }
      return '0 0 ' + this.width + ' ' + this.height;
    },
    ratio: function ratio() {
      if (!this.icon) {
        return 1;
      }
      var _icon = this.icon,
          width = _icon.width,
          height = _icon.height;

      return Math.max(width, height) / 16;
    },
    width: function width() {
      return this.childrenWidth || this.icon && this.icon.width / this.ratio * this.normalizedScale || 0;
    },
    height: function height() {
      return this.childrenHeight || this.icon && this.icon.height / this.ratio * this.normalizedScale || 0;
    },
    style: function style() {
      if (this.normalizedScale === 1) {
        return false;
      }
      return {
        fontSize: this.normalizedScale + 'em'
      };
    },
    raw: function raw() {
      // generate unique id for each icon's SVG element with ID
      if (!this.icon || !this.icon.raw) {
        return null;
      }
      var raw = this.icon.raw;
      var ids = {};
      raw = raw.replace(/\s(?:xml:)?id=(["']?)([^"')\s]+)\1/g, function (match, quote, id) {
        var uniqueId = getId();
        ids[id] = uniqueId;
        return ' id="' + uniqueId + '"';
      });
      raw = raw.replace(/#(?:([^'")\s]+)|xpointer\(id\((['"]?)([^')]+)\2\)\))/g, function (match, rawId, _, pointerId) {
        var id = rawId || pointerId;
        if (!id || !ids[id]) {
          return match;
        }

        return '#' + ids[id];
      });

      return raw;
    }
  },
  mounted: function mounted() {
    var _this = this;

    if (!this.name && this.name !== null && this.$children.length === 0) {
      console.warn('Invalid prop: prop "name" is required.');
      return;
    }

    if (this.icon) {
      return;
    }

    var width = 0;
    var height = 0;
    this.$children.forEach(function (child) {
      child.outerScale = _this.normalizedScale;

      width = Math.max(width, child.width);
      height = Math.max(height, child.height);
    });
    this.childrenWidth = width;
    this.childrenHeight = height;
    this.$children.forEach(function (child) {
      child.x = (width - child.width) / 2;
      child.y = (height - child.height) / 2;
    });
  },
  register: function register(data) {
    for (var name in data) {
      var icon = data[name];
      var _icon$paths = icon.paths,
          paths = _icon$paths === undefined ? [] : _icon$paths,
          d = icon.d,
          _icon$polygons = icon.polygons,
          polygons = _icon$polygons === undefined ? [] : _icon$polygons,
          points = icon.points;


      if (d) {
        paths.push({ d: d });
      }

      if (points) {
        polygons.push({ points: points });
      }

      icons[name] = assign({}, icon, {
        paths: paths,
        polygons: polygons
      });
    }
  },

  icons: icons
};


var cursor = 0xd4937;
function getId() {
  return 'fa-' + (cursor++).toString(16);
}
</script>

<style>
.fa-icon {
  display: inline-block;
  fill: currentColor;
}

.fa-flip-horizontal {
  transform: scale(-1, 1);
}

.fa-flip-vertical {
  transform: scale(1, -1);
}

.fa-spin {
  animation: fa-spin 1s 0s infinite linear;
}

.fa-inverse {
  color: #fff;
}

.fa-pulse {
  animation: fa-spin 1s infinite steps(8);
}

@keyframes fa-spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
