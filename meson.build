project('capstone', 'c',
  version: '5.0.0',
)

available_archs = {
  'arm': ['ARM', ['ARMDisassembler.c', 'ARMInstPrinter.c', 'ARMMapping.c', 'ARMModule.c']],
  'arm64': ['AArch64', ['AArch64BaseInfo.c', 'AArch64Disassembler.c', 'AArch64InstPrinter.c', 'AArch64Mapping.c', 'AArch64Module.c']],
  'bpf': ['BPF', ['BPFDisassembler.c', 'BPFInstPrinter.c', 'BPFMapping.c', 'BPFModule.c']],
  'evm': ['EVM', ['EVMDisassembler.c', 'EVMInstPrinter.c', 'EVMMapping.c', 'EVMModule.c']],
  'm680x': ['M680X', ['M680XDisassembler.c', 'M680XInstPrinter.c', 'M680XModule.c']],
  'm68k': ['M68K', ['M68KDisassembler.c', 'M68KInstPrinter.c', 'M68KModule.c']],
  'mips': ['Mips', ['MipsDisassembler.c', 'MipsInstPrinter.c', 'MipsMapping.c', 'MipsModule.c']],
  'mos65xx': ['MOS65XX', ['MOS65XXDisassembler.c', 'MOS65XXModule.c']],
  'powerpc': ['PowerPC', ['PPCDisassembler.c', 'PPCInstPrinter.c', 'PPCMapping.c', 'PPCModule.c']],
  'riscv': ['RISCV', ['RISCVDisassembler.c', 'RISCVInstPrinter.c', 'RISCVMapping.c', 'RISCVModule.c']],
  'sparc': ['Sparc', ['SparcDisassembler.c', 'SparcInstPrinter.c', 'SparcMapping.c', 'SparcModule.c']],
  'sysz': ['SystemZ', ['SystemZDisassembler.c', 'SystemZInstPrinter.c', 'SystemZMCTargetDesc.c', 'SystemZMapping.c', 'SystemZModule.c']],
  'tms320c64x': ['TMS320C64x', ['TMS320C64xDisassembler.c', 'TMS320C64xInstPrinter.c', 'TMS320C64xMapping.c', 'TMS320C64xModule.c']],
  'wasm': ['WASM', ['WASMDisassembler.c', 'WASMInstPrinter.c', 'WASMMapping.c', 'WASMModule.c']],
  'x86': ['X86', ['X86ATTInstPrinter.c', 'X86Disassembler.c', 'X86DisassemblerDecoder.c', 'X86InstPrinterCommon.c', 'X86IntelInstPrinter.c', 'X86Mapping.c', 'X86Module.c']],
  'xcore': ['XCore', ['XCoreDisassembler.c', 'XCoreInstPrinter.c', 'XCoreMapping.c', 'XCoreModule.c']],
}

desired_archs = get_option('archs')
if desired_archs.length() == 0 or 'all' in desired_archs
  desired_archs = available_archs.keys()
endif

headers = []
header_names = [
  'arm.h',
  'arm64.h',
  'bpf.h',
  'capstone.h',
  'evm.h',
  'm680x.h',
  'm68k.h',
  'mips.h',
  'mos65xx.h',
  'platform.h',
  'ppc.h',
  'riscv.h',
  'sparc.h',
  'systemz.h',
  'tms320c64x.h',
  'wasm.h',
  'x86.h',
  'xcore.h',
]
foreach name : header_names
  headers += 'include' / 'capstone' / name
endforeach

sources = [
  'MCInst.c',
  'MCInstrDesc.c',
  'MCRegisterInfo.c',
  'SStream.c',
  'cs.c',
  'utils.c',
]
foreach arch_id : desired_archs
  spec = available_archs[arch_id]
  arch_name = spec[0]
  arch_sources = spec[1]
  foreach source : arch_sources
    sources += 'arch' / arch_name / source
  endforeach
endforeach

defines = []
foreach arch_id : desired_archs
  defines += '-DCAPSTONE_HAS_' + arch_id.to_upper()
endforeach
available_flags = [
  'use_sys_dyn_mem',
  'diet',
  'x86_reduce',
  'x86_att_disable',
  'has_osxkernel',
]
foreach flag : available_flags
  if get_option(flag)
    defines += '-DCAPSTONE_' + flag.to_upper()
  endif
endforeach
ndebug = get_option('b_ndebug')
if ndebug == 'false' or (ndebug == 'if-release' and get_option('debug'))
  defines += '-DCAPSTONE_DEBUG'
endif

install_headers(headers, subdir: 'capstone')

include_dirs = include_directories('include')

capstone = library('capstone', sources,
  c_args: defines,
  include_directories: include_dirs,
  install: true,
)
capstone_dep = declare_dependency(
  link_with: capstone,
  include_directories: include_directories('include' / 'capstone'),
  variables: {'includedir': meson.current_source_dir() / 'include'},
)

pkg = import('pkgconfig')
pkg.generate(
  name: 'capstone',
  description: 'Capstone disassembly engine',
  url: 'https://www.capstone-engine.org',
  subdirs: ['capstone'],
  libraries: [capstone],
)

cli_option = get_option('cli')
if cli_option.auto()
  cli_enabled = not meson.is_subproject()
else
  cli_enabled = cli_option.enabled()
endif
if cli_enabled
  subdir('cstool')
endif
